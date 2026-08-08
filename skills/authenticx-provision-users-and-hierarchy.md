---
name: Provision Authenticx users, agents, roles and hierarchy
description: >-
  Administer an Authenticx organization programmatically — build the hierarchy tree, create agents and users,
  read the role/permission catalog, and grant per-hierarchy permissions. Includes the parallel SCIM 2.0 path
  for identity-provider-driven provisioning.
api: openapi/authenticx-acxapi-openapi.yml
generated: '2026-08-06'
method: generated
source: openapi/authenticx-acxapi-openapi.yml
operations:
  - GET /Hierarchy/All
  - POST /Hierarchy
  - PUT /Hierarchy/{HierarchyId}
  - GET /Roles/All
  - POST /User
  - GET /User
  - GET /User/All
  - GET /User/{UserId}
  - PUT /User/{UserId}
  - POST /Agent
  - GET /Agent/All
  - GET /Agent/{AgentId}
  - PUT /Agent/{AgentId}
  - GET /UserHierarchy/{UserId}/All
  - GET /UserHierarchy/{UserId}/{HierarchyId}
  - PUT /UserHierarchy/{UserId}/{HierarchyId}
  - GET /scim/v2/ServiceProviderConfig
  - GET /scim/v2/Schemas
  - GET /scim/v2/ResourceTypes
  - GET /scim/v2/Users
  - POST /scim/v2/Users
  - PATCH /scim/v2/Users/{UserId}
  - DELETE /scim/v2/Users/{UserId}
operation_id_note: >-
  The AcxAPI OpenAPI declares no operationId on any operation. Every step below is bound by METHOD + path,
  verified against openapi/authenticx-acxapi-openapi.yml.
---

# Provision users, agents, roles and hierarchy

Authenticate first — see `skills/authenticx-upload-and-retrieve-insights.md` step 1. One scope, `acxapi`,
grants this entire administrative surface including SCIM. There is **no read-only credential**: any client that
can read conversations can also create users and change permissions. Treat AcxAPI credentials accordingly.

## Choose a path first

Authenticx ships **two independent user models** and they are not the same schema:

| Path | Use when | Endpoints |
|---|---|---|
| Native | Your app drives provisioning directly | `/User`, `/Agent`, `/Hierarchy`, `/UserHierarchy` |
| SCIM 2.0 | Your IdP (Okta, Entra, OneLogin) drives provisioning | `/scim/v2/Users` |

Pick one and stay on it. Nothing in the docs reconciles the two representations, so driving both against the
same people is asking for drift.

## 1. Build or read the hierarchy

The hierarchy is the organization tree. It scopes conversations, agents and every permission grant, so it comes
first.

- `GET /Hierarchy/All?IsActive=true` — returns the whole tree. `children` is self-referencing; walk it
  recursively. Each node carries `id`, `code`, `name`, `structureLevelName`, `category`, `subCategory`, and
  three contact lists (`administrativeContacts`, `escalationContacts`, `managerContacts`).
- `POST /Hierarchy` — add a node.
- `PUT /Hierarchy/{HierarchyId}` — update one.

Note there is **no pagination** on `/Hierarchy/All` and **no delete**. Deactivation is via update.

Both `id` (GUID) and `code` (your internal organization code) work as references elsewhere — `code` is the one
worth using in upload metadata, because it is stable and human-meaningful.

## 2. Read the role catalog

`GET /Roles/All?IsActive=true` returns every role with its `permissions[]` and each permission's description.
Roles are identified by **`name`, not an id**. Read this before creating users — you assign `roleName` on the
user record and there is no endpoint to create a role, so you can only pick from what exists.

## 3. Create users

- `POST /User` — create. `GET /User?UserName=<name>` — look up by username. `GET /User/{UserId}` — by id.
  `GET /User/All?IsActive=true` — everyone. `PUT /User/{UserId}` — update.
- The user record carries `organizationId`, `userName`, `email`, `emailConfirmed`, `roleName`, `disabled`,
  `samlProviderUserCode`, `lastLogin` and `agentId`.
- There is **no delete**. Deactivate by setting `disabled`.
- `samlProviderUserCode` is the hook for SSO-federated users.

## 4. Create agents and link them

Agents (the people on the calls) are a separate entity from users (the people in the platform).

- `POST /Agent` — create. A 400 here means "a userId was provided but did not correspond to an existing,
  active user in your organization" — create the user first.
- `GET /Agent/All?IsActive=true`, `GET /Agent/{AgentId}`, `PUT /Agent/{AgentId}`.
- `Agent.userId` → User, and `User.agentId` → Agent. The two point at each other; set them consistently.
- `Agent.managerId` → another Agent, so the manager chain is self-referencing.
- `agentCode` is the value you put in upload metadata (`AgentCode`) to attach an interaction to an agent
  already in the system.

## 5. Grant per-hierarchy permissions

Role gives a user a baseline; hierarchy permissions decide **which slice of the org** they see.

- `GET /UserHierarchy/{UserId}/All?IsActive=true` — every hierarchy plus this user's `accessLevel` and
  `permissions[]` on each.
- `GET /UserHierarchy/{UserId}/{HierarchyId}` — one node.
- `PUT /UserHierarchy/{UserId}/{HierarchyId}` — set them. A 404 means the user or the hierarchy id does not
  exist; check both before assuming a permissions problem.

Grant per node. There is no bulk grant and no inheritance flag documented, so a deep tree means a call per
node — batch them yourself and rate-limit your own loop.

## 6. Or: SCIM 2.0 from your IdP

`/scim/v2/` is a conformant SCIM 2.0 surface (RFC 7643/7644) responding `application/scim+json`.

1. `GET /scim/v2/ServiceProviderConfig` — what this server supports (filter, bulk, patch, auth schemes). Point
   your IdP here first; it returns 401 unauthenticated, so send the bearer token.
2. `GET /scim/v2/Schemas` and `GET /scim/v2/ResourceTypes` — the attribute contract.
3. `GET /scim/v2/Users` — supports `filter`, `sortBy`, `sortOrder`, `startIndex`, `count`, `attributes`,
   `excludedAttributes`. Note this surface pages with **`startIndex`/`count`**, not the `LastId`/`PageSize`
   cursor the rest of AcxAPI uses.
4. `POST /scim/v2/Users` → **201**. A **409** means one or more required fields already belong to an existing
   user — reconcile, do not retry.
5. `PATCH /scim/v2/Users/{UserId}` for partial updates, `PUT` for replace, `DELETE` → **204**.

SCIM is also the only part of AcxAPI with **typed errors** — `application/scim+json` error bodies per RFC 7644
§3.12. Everywhere else you get a bare string.

## Things that will bite you

- **One scope for everything.** `acxapi` covers conversation reads *and* user creation *and* permission
  changes. Scope your credential's blast radius operationally, because OAuth will not do it for you.
- **No deletes on native users, agents or hierarchies.** Only deactivation.
- **Roles are read-only** and keyed by name.
- **No idempotency key.** Re-running provisioning is not safe by contract; check for existence
  (`GET /User?UserName=`) before creating.
- **404 on collections.** `GET /Agent/All` and `GET /Hierarchy/All` declare 404 where an empty 200 collection
  would be conventional. Handle 404 as "empty", not as an error.
