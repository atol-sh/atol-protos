# Atol Protos

The public, SDK-facing API contract for [Atol](https://atol.sh) — identity and
authorization for developers who ship. This module is the single source of truth
that every Atol SDK and the control plane generate their bindings from.

## Package `atol.api.v1`

The data-plane surface that embedded SDKs consume:

| Service | Purpose |
|---------|---------|
| `AccessService` | Write-through relationship-tuple management (`GrantAccess`, `RevokeAccess`). |
| `DPAgentService` | Bootstrap snapshots, decision-log ingestion, live mutation streaming, and device-divergence reporting. |

Authorization *reads* are evaluated locally inside the SDK from its bootstrapped
in-memory store — only writes and control-plane synchronization use these RPCs.
Internal admin APIs (org, user, trust, policy, audit) are intentionally **not**
part of this module.

Identifiers follow Zanzibar conventions: subjects and objects are `type:id`
(for example `user:alice`, `document:readme`).

## Using the SDK

Generated SDKs are available from the BSR. Pick your language from the **SDKs**
tab above, or grab the Go module directly:

```bash
go get buf.build/gen/go/atol-sh/atol-protos/...
```

Most Go users consume the higher-level [`atol-sdk-go`](https://github.com/atol-sh/atol-sdk-go)
SDK, which embeds these stubs along with the in-process authorization engines.

## Stability

The wire contract is versioned by the package suffix (`v1`) and by git tags on
the [source repository](https://github.com/atol-sh/atol-protos). Breaking
changes are caught in CI with `buf breaking`.

License: Apache 2.0.
