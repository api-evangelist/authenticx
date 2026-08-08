---
name: Reconcile Authenticx pharmacovigilance and model results with a downstream safety system
description: >-
  Pull adverse-event, safety-event and product-quality-complaint classifications out of Authenticx, join them
  to your own conversation ids, and reconcile the export receipts against your downstream safety or quality
  system. The regulated life-sciences flow AcxAPI's Receipts and ModelResults endpoints exist to serve.
api: openapi/authenticx-acxapi-openapi.yml
generated: '2026-08-06'
method: generated
source: openapi/authenticx-acxapi-openapi.yml + https://authenticx.readme.io/reference/receipts
operations:
  - GET /Conversations/Classifiers
  - GET /Hierarchy/All
  - GET /ModelResults
  - GET /ModelResults/Conversation
  - GET /Receipts
  - GET /Metadata
  - GET /Evaluations
  - GET /Evaluations/Modules
  - GET /Workflows
operation_id_note: >-
  The AcxAPI OpenAPI declares no operationId on any operation. Every step below is bound by METHOD + path,
  verified against openapi/authenticx-acxapi-openapi.yml.
---

# Reconcile pharmacovigilance results and export receipts

This flow answers one question a regulated life-sciences team has to be able to answer on demand: *for every
conversation in a window, what did Authenticx classify, what did we export downstream, and did the downstream
system acknowledge it?*

Authenticate first — see `skills/authenticx-upload-and-retrieve-insights.md` step 1.

## 1. Discover what you are allowed to ask for

Model and classifier ids are per-organization. Never hard-code them.

- `GET /Conversations/Classifiers` — returns `id`, `name`, `classifierType`, `categoryName`, `reportable`,
  `groupable`. Supports `CategoryNames` and `ClassifierTypes` filters, and pages with `PageSize` + `LastId`.
  **These ids are also the valid `modelIds` for `/ModelResults`.**
- `GET /Hierarchy/All` — the valid `hierarchyCodes`.

If you call `/ModelResults/Conversation` with an unknown `modelIds`, the 400 body helpfully returns the
available active model ids for your organization. That is a usable discovery fallback, but prefer the
classifier endpoint.

## 2. Sweep model results for a window

`GET /ModelResults` with `startDate`, `endDate`, `modelIds`, `hierarchyCodes`, `pageSize`, `lastId`,
`includeUnprocessedMedia`.

Watch these:

- **Casing.** This endpoint uses camelCase (`lastId`, `pageSize`); most of AcxAPI uses PascalCase. Do not share
  a pagination helper across both without normalizing.
- **Hard floor.** Any `startDate` earlier than **2025-10-01** is silently clamped to 2025-10-01. You will get a
  200 with a narrower window than you asked for and no warning. Compute your own expected range and assert it.
- `includeUnprocessedMedia=true` widens the result to conversations whose `mediaStatus` is `Processed`,
  `NotProcessed` or `FailedToProcess`. Unprocessed rows return `modelResults: null` — for reconciliation you
  want this on, so gaps show up as rows rather than as absence.

Each row carries `conversationId`, `clientCallId`, `eventType`, `eventTime`, `mediaStatus`, `modelResults[]`,
`priorResults[]` and `emissionReceipt` (`authenticxReceiptId`, `customerReceiptId`, `eventTime`).

`priorResults[]` is the audit trail — a classification that changed after human review appears here, so a
reconciliation that reads only the current `modelResults` will silently miss re-adjudications.

## 3. Look up specific conversations

`GET /ModelResults/Conversation` when you already know the ids.

- `modelIds` is **required**.
- At least one of `conversationIds` or `clientCallIds` must be supplied. Supply both and you get the union.
- **Maximum 100 ids per parameter.** Chunk your input.
- A **501** here means the endpoint is not enabled for your organization ("Please contact Authenticx Help Desk
  support"). That is an entitlement signal, not an outage — do not retry it.

`clientCallIds` is the important one: it lets you drive reconciliation from *your* source-system identifiers
rather than having to store Authenticx GUIDs.

## 4. Pull the export receipts

`GET /Receipts` with `DateReference`, `StartDate`, `EndDate`, `Status`, `HierarchyCodes`, `PageSize`, `LastId`.

Each receipt is the record of one export attempt to your downstream system:

| Field | Meaning |
|---|---|
| `ConversationId` | Joins to `Metadata.Id` and to Insights `ConversationId` |
| `ClientCallId` | Your external telephony reference |
| `DateTimeReceived` | When the interaction arrived in Authenticx |
| `DateTimeTransmitted` | When the export attempt was transmitted |
| `ReceiptId` | The id returned by the downstream system |
| `uid` | The external system UID returned from the export endpoint |
| `SafetyEvent` | Safety-event classification result from the export |
| `AdverseEvent` | Adverse-event classification result from the export |
| `ProductQualityComplaint` | Product-quality-complaint result from the export |

## 5. Do the three-way reconciliation

For a window:

1. `GET /Metadata` (or `/Conversations/Insights`) → the set of conversations that exist.
2. `GET /ModelResults` with `includeUnprocessedMedia=true` → what was classified, plus what failed to process.
3. `GET /Receipts` → what was transmitted and acknowledged.

Then assert:

- **Classified but no receipt** → a conversation with a `SafetyEvent`/`AdverseEvent`/`ProductQualityComplaint`
  hit in ModelResults and no matching `ConversationId` in Receipts. This is the finding that matters — a
  potential unreported event.
- **Receipt with no `ReceiptId`/`uid`** → transmitted but not acknowledged downstream.
- **`mediaStatus: FailedToProcess`** → never classified at all. Not a false negative; an unprocessed input.
- **`priorResults[]` non-empty** → the classification changed. Re-check whether the export reflects the current
  value.

Join on `ConversationId` first and fall back to `ClientCallId`. Both are present on Receipts and ModelResults.

## 6. Human-review context

- `GET /Evaluations` and `GET /Evaluations/Modules` give the QA evaluations, each embedding the full
  `metadata` object (so `Metadata.Id` is your conversation join) plus `agent`, `analyst`, `questions`,
  `callSummary` and `customerType`.
- `GET /Workflows` gives workflow status per `interactionId` and `evaluationNumber` — use it to show whether a
  flagged conversation is still in review.

## Things that will bite you

- **Poll; there is no event feed.** Authenticx publishes no webhooks and no AsyncAPI. A reconciliation job is a
  scheduled sweep, and the window you choose is the only guarantee you have of not missing a late-arriving
  result. Overlap your windows.
- **Date-range ceilings differ per endpoint.** `/Conversations/Insights` caps at 7 days unfiltered / 31 days
  filtered; `/ModelResults` floors at 2025-10-01. Neither is signalled in the response.
- **No rate-limit budget is published.** Only `POST /Media/Upload` declares 429, but that does not mean the
  read endpoints are unlimited — it means the limit is undocumented. Throttle your sweep yourself.
- **Errors carry no codes.** Outside SCIM there is no response schema on any 4xx. Log the full body.
- **This is regulated data.** Everything above is PHI under HIPAA. Authenticx publishes SOC 2 Type I/II and
  HIPAA/GDPR/CCPA alignment (https://authenticx.com/privacy-security) but no trust center or attestation
  portal — request the report through your account team.
