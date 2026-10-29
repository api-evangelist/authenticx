# Authenticx

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Authenticx is a healthcare conversation intelligence platform that ingests contact-center interactions — call audio, chat transcripts, and email — and applies speech analytics and machine-learning classifiers to surface patient and member experience signals, quality-assurance scoring, and pharmacovigilance / adverse-event detection. Its AcxAPI is a REST API described by a live OpenAPI 3.0.1 definition covering conversation insights, transcriptions, model results, evaluations, metadata, workflows, audio and text media upload, agent, hierarchy and role administration, and SCIM 2.0 user provisioning. Interaction ingestion also runs through out-of-the-box connectors for Genesys Cloud, Amazon Connect, NICE CXone, Five9, Vonage Contact Center and Talkdesk, plus Salesforce/MuleSoft conversation enrichment and SFTP batch delivery. Authentication is OAuth 2.0 client credentials against an OpenID Connect provider, with separate production and experimental (staging) hosts.

**Areas:** Conversation Intelligence · Healthcare · Speech Analytics · Contact Center · Customer Experience · Quality Assurance · Pharmacovigilance · Patient Experience · Transcription · Life Sciences · SCIM · Authentication

## Authenticx on APIs.io

Everything profiled for Authenticx is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/authenticx/](https://apis.io/providers/authenticx/)** — or go straight to one of the APIs or properties below.

### APIs (18)

- [Authenticx Agent API](https://apis.io/apis/authenticx/authenticx-agent-api/) — The Agent API from Authenticx — 3 operation(s) for agent.
- [Authenticx Conversations API](https://apis.io/apis/authenticx/authenticx-conversations-api/) — The Conversations API from Authenticx — 4 operation(s) for conversations.
- [Authenticx Evaluations API](https://apis.io/apis/authenticx/authenticx-evaluations-api/) — The Evaluations API from Authenticx — 2 operation(s) for evaluations.
- [Authenticx Hierarchy API](https://apis.io/apis/authenticx/authenticx-hierarchy-api/) — The Hierarchy API from Authenticx — 3 operation(s) for hierarchy.
- [Authenticx Interactions API](https://apis.io/apis/authenticx/authenticx-interactions-api/) — The Interactions API from Authenticx — 2 operation(s) for interactions.
- [Authenticx Media API](https://apis.io/apis/authenticx/authenticx-media-api/) — The Media API from Authenticx — 1 operation(s) for media.
- [Authenticx Metadata API](https://apis.io/apis/authenticx/authenticx-metadata-api/) — The Metadata API from Authenticx — 2 operation(s) for metadata.
- [Authenticx Model Results API](https://apis.io/apis/authenticx/authenticx-modelresults-api/) — The ModelResults API from Authenticx — 2 operation(s) for modelresults.
- [Authenticx Receipts API](https://apis.io/apis/authenticx/authenticx-receipts-api/) — The Receipts API from Authenticx — 1 operation(s) for receipts.
- [Authenticx Roles API](https://apis.io/apis/authenticx/authenticx-roles-api/) — The Roles API from Authenticx — 1 operation(s) for roles.
- [Authenticx (Scim) ResourceTypes API](https://apis.io/apis/authenticx/authenticx-scim-resourcetypes-api/) — The (Scim) ResourceTypes API from Authenticx — 2 operation(s) for (scim) resourcetypes.
- [Authenticx (Scim) Schemas API](https://apis.io/apis/authenticx/authenticx-scim-schemas-api/) — The (Scim) Schemas API from Authenticx — 2 operation(s) for (scim) schemas.
- [Authenticx (Scim) ServiceProviderConfig API](https://apis.io/apis/authenticx/authenticx-scim-serviceproviderconfig-api/) — The (Scim) ServiceProviderConfig API from Authenticx — 1 operation(s) for (scim) serviceproviderconfig.
- [Authenticx (Scim) Users API](https://apis.io/apis/authenticx/authenticx-scim-users-api/) — The (Scim) Users API from Authenticx — 2 operation(s) for (scim) users.
- [Authenticx Text Media API](https://apis.io/apis/authenticx/authenticx-textmedia-api/) — The TextMedia API from Authenticx — 1 operation(s) for textmedia.
- [Authenticx User API](https://apis.io/apis/authenticx/authenticx-user-api/) — The User API from Authenticx — 3 operation(s) for user.
- [Authenticx User Hierarchy API](https://apis.io/apis/authenticx/authenticx-userhierarchy-api/) — The UserHierarchy API from Authenticx — 2 operation(s) for userhierarchy.
- [Authenticx Workflows API](https://apis.io/apis/authenticx/authenticx-workflows-api/) — The Workflows API from Authenticx — 1 operation(s) for workflows.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Authenticx Emissions Webhooks](https://apis.io/asyncapis/authenticx/authenticx-emissions-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [authenticx-mcp.yml](https://apis.io/servers/authenticx/authenticx-mcp-yml/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Authenticx Agentic Access](https://apis.io/agentic-access/authenticx/authenticx-agentic-access/)

#### Collections (18)

Postman and Open Collection request collections.

- [AcxApi Production Agent API](https://apis.io/collections/authenticx/open-authenticx-agent-api/)
- [AcxApi Production Conversations API](https://apis.io/collections/authenticx/open-authenticx-conversations-api/)
- [AcxApi Production Evaluations API](https://apis.io/collections/authenticx/open-authenticx-evaluations-api/)
- [AcxApi Production Hierarchy API](https://apis.io/collections/authenticx/open-authenticx-hierarchy-api/)
- [AcxApi Production Interactions API](https://apis.io/collections/authenticx/open-authenticx-interactions-api/)
- [AcxApi Production Media API](https://apis.io/collections/authenticx/open-authenticx-media-api/)
- [AcxApi Production Metadata API](https://apis.io/collections/authenticx/open-authenticx-metadata-api/)
- [AcxApi Production Model Results API](https://apis.io/collections/authenticx/open-authenticx-modelresults-api/)
- [AcxApi Production Receipts API](https://apis.io/collections/authenticx/open-authenticx-receipts-api/)
- [AcxApi Production Roles API](https://apis.io/collections/authenticx/open-authenticx-roles-api/)
- [AcxApi Production (Scim) ResourceTypes (Scim) ResourceTypes API](https://apis.io/collections/authenticx/open-authenticx-scim-resourcetypes-api/)
- [AcxApi Production (Scim) Schemas (Scim) Schemas API](https://apis.io/collections/authenticx/open-authenticx-scim-schemas-api/)
- [AcxApi Production (Scim) ServiceProviderConfig (Scim) ServiceProviderConfig API](https://apis.io/collections/authenticx/open-authenticx-scim-serviceproviderconfig-api/)
- [AcxApi Production (Scim) Users (Scim) Users API](https://apis.io/collections/authenticx/open-authenticx-scim-users-api/)
- [AcxApi Production Text Media API](https://apis.io/collections/authenticx/open-authenticx-textmedia-api/)
- [AcxApi Production User API](https://apis.io/collections/authenticx/open-authenticx-user-api/)
- [AcxApi Production User Hierarchy API](https://apis.io/collections/authenticx/open-authenticx-userhierarchy-api/)
- [AcxApi Production Workflows API](https://apis.io/collections/authenticx/open-authenticx-workflows-api/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Authenticx Acxapi Overlay](https://apis.io/overlays/authenticx/authenticx-acxapi-overlay/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Authenticx Authentication](https://apis.io/security/authenticx/authenticx-authentication/)
- [Authenticx Domain Security](https://apis.io/security/authenticx/authenticx-domain-security/)
- [Authenticx Trust Center](https://apis.io/security/authenticx/authenticx-trust-center/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Authenticx Scopes](https://apis.io/scopes/authenticx/authenticx-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Authenticx Plans Pricing](https://apis.io/plans/authenticx/authenticx-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Authenticx Rate Limits](https://apis.io/rate-limits/authenticx/authenticx-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://authenticx.com/)
- [Documentation](https://authenticx.readme.io/docs/getting-started)
- [Getting started](https://authenticx.readme.io/docs/getting-started)
- [API reference](https://authenticx.readme.io/reference/acxapi)
- [Blog](https://authenticx.com/resources)
- [Compliance](https://authenticx.com/privacy-security)
- [Trust centre](https://authenticx.com/privacy-security)
- [Terms of service](https://authenticx.com/terms)
- [Privacy policy](https://authenticx.com/privacy)
- [GitHub](https://github.com/beacx)

## API Evangelist rating

**Kin Score 50.2** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 73.2 |
| Contract quality | 61.8 |
| Contract governance | 18.2 |
| Operational transparency | 10.5 |
| Developer ergonomics | 47.0 |
| Access clarity | 43.4 |

**Agent readiness 43.4** — agent-ready (gated down from agent-native)

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | served (partial credit) |
| Idempotency | — |
| Error Semantics | verified |
| OpenAPI Examples | partial |
| Rate Limit Signal | verified |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | partial |
| Operation Distinctiveness | — |

**Regulatory alignment 36.7** — Health

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/authenticx/](https://apis.io/providers/authenticx/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Authenticx: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/authenticx/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
