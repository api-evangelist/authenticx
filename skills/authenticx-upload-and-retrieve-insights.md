---
name: Upload a conversation to Authenticx and retrieve its insights
description: >-
  Authenticate to AcxAPI, upload one call recording or chat transcript, poll for processing to finish, then
  pull the classifier results and transcript back out. The end-to-end round trip every Authenticx integration
  starts with.
api: openapi/authenticx-acxapi-openapi.yml
generated: '2026-08-06'
method: generated
source: openapi/authenticx-acxapi-openapi.yml + https://authenticx.readme.io/docs/acxapi
operations:
  - POST /connect/token
  - POST /Media/Upload
  - POST /TextMedia/Upload
  - GET /Metadata
  - GET /Conversations/Insights
  - GET /Conversations/Transcriptions/{conversationId}
operation_id_note: >-
  The AcxAPI OpenAPI declares no operationId on any operation. Every step below is bound by METHOD + path,
  verified against openapi/authenticx-acxapi-openapi.yml.
---

# Upload a conversation and retrieve its insights

## Before you start

You need a `client_id` and `client_secret`. There is **no self-serve signup** — Authenticx issues credentials
during onboarding. Pick your environment:

| Environment | Base URL | Token URL |
|---|---|---|
| Experimental (use this first) | `https://api.authcx.com` | `https://api.authcx.com/connect/token` |
| Production | `https://api.beauthenticx.com` | `https://api.beauthenticx.com/connect/token` |

Nothing in the token tells you which environment it belongs to. If you mix hosts you get an auth failure, not
a helpful error — set the base URL once and derive the token URL from it.

## 1. Get a token

`POST {base}/connect/token`, `application/x-www-form-urlencoded`:

```
client_id=<id>&client_secret=<secret>&grant_type=client_credentials&scope=acxapi
```

Returns `{ "access_token": ..., "token_type": "Bearer", "expires_in": 3600, "scope": "acxapi" }`.

- The token lasts **3600 seconds** and there is **no refresh token** on this flow. Cache it, and re-request
  when it is within a minute of expiry.
- `client_secret_basic` also works: base64 `client-id:client-secret` in `Authorization: Basic`, with
  `grant_type` and `scope` still in the form body.

Send `Authorization: Bearer <access_token>` on every subsequent request.

## 2. Upload the interaction

Audio → `POST {base}/Media/Upload`. Text/chat JSON → `POST {base}/TextMedia/Upload`.

Both are `multipart/form-data` with exactly two named parts:

- `file` — the media file
- `metadata` — a **JSON string**, not a JSON part

Hard rules that cause most first-attempt failures:

- `metadata.FileName` must match the uploaded file name **exactly**, including extension and case. A mismatch
  is a 400 (`File extension in metadata file name and upload file must match!`).
- Max file size **2.5GB**. Audio: `.wav .mp3 .mp4 .ogg .opus .m4a`. Chat archives: `.tar .zip`. Text:
  `.json .json_chat`.
- `UploadType` is required on `/Media/Upload` — `"Audio"` or `"Chat"`.
- `LocaleId` defaults to `1033` (en-US); `2058` (es-MX) is the only other supported value.
- If you pass `HierarchyId` or `HierarchyCode`, it must be a real one — discover valid values first with
  `GET /Hierarchy/All`. An invalid value fails the whole upload.
- Use `ExtendedMetadataValues` (a dictionary). `ExtendedMetadata` (array of dictionaries) is deprecated.
- Set `ClientCallId` to your own telephony reference. This is the supported way to join Authenticx records back
  to your system later, via `GET /ModelResults/Conversation?clientCallIds=...`.

For large files, pass `signedUrl` instead of a `file` part (S3 or Azure Blob pre-signed download URL) and
Authenticx pulls the file itself.

A success returns `{ "Id": "<GUID>", "FileName": ..., "FileSizeKb": ... }`. **That `Id` is the conversation
id** — hold onto it.

### Retrying safely

There is **no idempotency key**. If an upload times out, do not blindly retry: AcxAPI rejects a duplicate with
400 `An interaction with this file name already exists.` — treat that specific 400 as *success on the previous
attempt*, not as a failure. `POST /Media/Upload` is also the one operation that can answer **429**, with no
`Retry-After` and no published quota; back off exponentially.

## 3. Wait for processing (poll — there are no webhooks)

Authenticx publishes no webhooks, callbacks, or event stream. Completion is discovered by polling.

`GET {base}/Conversations/Transcriptions/{conversationId}` is the clearest signal, but read the **body**, not
just the status code:

- **200 with a message** → still processing. Keep polling.
- **200 with transcriptions** → done.
- **400 with a message** → the conversation failed to process. Stop.

Poll on a backoff (30s → 5m). Alternatively `GET {base}/Metadata?ClientCallId=<yours>` and read `status`.

## 4. Read the insights

`GET {base}/Conversations/Insights` returns classifier results.

- Filter by `StartDate` / `EndDate` (with `DateReference` selecting which date field the window applies to).
- **Date-range ceiling:** with no classifier or hierarchy filter the window is capped at **7 days**. Supply at
  least one of `ClassifierIds`, `HierarchyIds`, `HierarchyCodes`, `ClassifierCategoryNames` or
  `ClassifierTypes` and it extends to **31 days**.
- Discover valid classifier ids with `GET /Conversations/Classifiers`; valid hierarchy ids and codes with
  `GET /Hierarchy/All`.
- If your filter list is too long for a query string, use the twin `POST /Conversations/Insights` with the same
  criteria in a JSON body.

Paginate with `PageSize` + `LastId`: pass the `lastId` of the final item in a page to get the next page. Stop
when a page returns fewer than `PageSize` items.

## 5. Pull transcripts in bulk

`POST {base}/Conversations/Transcriptions` takes up to **100** conversation ids per request (duplicates
ignored) and returns a split response:

- `transcriptions[]` — the successes
- `errors[]` — per-item failures, each with a `statusCode`: `404` not found, `400` failed to process, `200`
  still processing

A partial failure comes back inside a 200. Always iterate `errors[]`.

## Things that will bite you

- **Do not use `/Interactions`.** `GET /Interactions`, `POST /Interactions` and `GET /Interactions/{AmdID}` are
  marked `deprecated: true` in the spec. Use `/Conversations/Insights`.
- **Errors are not machine-readable.** No RFC 9457, no error codes, no response schema on any non-SCIM 4xx.
  Branch on status code plus string matching, and expect that to be brittle.
- **One conversation, five field names.** The same id is `Id` under Metadata, `Metadata.Id` under Evaluations,
  and `ConversationId` under Insights, Receipts and Transcriptions (`conversationId` in Transcriptions
  *requests*). See `conventions/authenticx-conventions.yml`.
- **Parameter casing is inconsistent.** Most endpoints use `LastId`/`PageSize`; `GET /ModelResults` uses
  `lastId`/`pageSize`.
- **501 means "not entitled".** `GET /ModelResults/Conversation` returns 501 when the endpoint is not enabled
  for your organization. Treat it as a licensing signal, not a server fault.
