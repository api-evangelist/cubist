---
name: Set up a scoped role with a policy and session
description: Create a least-privilege signing role, attach keys and a policy, and mint a scoped session token.
api: openapi/cubist-cubesigner-openapi-original.json
operations: [createRole, addKeysToRole, createPolicy, createRoleToken]
---

# Set up a scoped role with a policy (CubeSigner)

Use this to delegate signing to a least-privilege role governed by Policy-as-Code.

## Auth
- Send a signer **session token** in the `Authorization` header (`SignerAuth`) with
  scopes `manage:policy:create`, `manage:key:create` and role-management scopes.
  See `scopes/cubist-scopes.yml`.

## Steps
1. **Create a role** — `createRole` (`POST /v0/org/{org_id}/roles`). Returns `role_id`.
2. **Attach keys** — `addKeysToRole` (`PUT /v0/org/{org_id}/roles/{role_id}/add_keys`)
   to grant the role signing over specific keys.
3. **Create a policy** — `createPolicy` (`POST /v0/org/{org_id}/policies`) encoding the
   allow/deny rules (e.g. allowed contacts, value ceilings, MFA requirements).
4. **Mint a role token** — `createRoleToken`
   (`POST /v0/org/{org_id}/roles/{role_id}/tokens`) to get a scoped session for a
   service that should only sign within the role's bounds.

## Conventions & errors
- Policy evaluation runs on every signing request; denials carry a `policy_eval`
  tree. Envelope + codes: `errors/cubist-error-codes.yml`.
- Prefer the narrowest scope set on each role token — see `conventions/cubist-conventions.yml`.
