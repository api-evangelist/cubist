---
name: Create a key and sign an EVM transaction
description: Provision a CubeSigner-managed EVM key and produce a signature, handling MFA step-up.
api: openapi/cubist-cubesigner-openapi-original.json
operations: [createKey, eip191Sign, eip712Sign]
---

# Create a key and sign an EVM transaction (CubeSigner)

Use this to mint an HSM-backed EVM signing key and sign with it.

## Auth
- Authenticate with an OIDC token, then exchange it for a signer **session token**
  (`cs token create`, or the SDK). Send it in the `Authorization` header (`SignerAuth`).
- The session token must carry the scopes `manage:key:create` and the relevant
  signing scope. See `scopes/cubist-scopes.yml`.

## Steps
1. **Create the key** — `createKey` (`POST /v0/org/{org_id}/keys`). Specify the
   EVM key type. The response returns the `key_id` / public address.
2. **Sign a message** — `eip191Sign` (`POST /v0/org/{org_id}/evm/eip191/sign/{pubkey}`)
   for personal-sign, or `eip712Sign` (`POST /v0/org/{org_id}/evm/eip712/sign/{pubkey}`)
   for typed data. Pass the key's `pubkey` in the path.

## Conventions & errors
- **MFA step-up:** if a policy requires approval, the sign call returns **HTTP 202**
  with an `accepted` MfaRequired body. Approve it (`mfaVote*`) and retry.
  See `conventions/cubist-conventions.yml`.
- **Policy denials** return an error with a `policy_eval` tree. Error envelope and
  codes: `errors/cubist-error-codes.yml`.
