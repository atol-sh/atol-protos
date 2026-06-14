# Atol Protos

Canonical, public-facing Protocol Buffer definitions for the [Atol](https://atol.sh)
SDK contract. This is the single source of truth for the data-plane API that
SDKs consume; the control plane and every SDK generate their bindings from here.

- **Repo:** `atol-sh/atol-protos`
- **License:** Apache 2.0
- **Tooling:** [buf](https://buf.build)

## Scope

This repo holds only the **SDK-facing** contract. Internal admin/management
APIs (org, user, trust, policy, audit) are intentionally **not** published here.

```
atol/api/v1/
├─ access.proto     # AccessService: GrantAccess / RevokeAccess (write-through tuples)
└─ dpagent.proto    # DPAgentService: bootstrap, decision-log ingestion, mutation
                    #   streaming, session device snapshot, divergence reporting
```

Package: `atol.api.v1`. Only `google/protobuf` well-known types are imported.

## Generating bindings

```bash
buf lint                  # validate against STANDARD rules
buf build                 # compile to a FileDescriptorSet
buf generate              # generate Go + Connect-go into ./gen (gitignored)
```

`buf.gen.yaml` is a reference Go config. Each SDK generates into its own module
path: the Go SDK overrides `go_package` via managed mode in its own
`buf.gen.yaml`, so the explicit `go_package` here (`atol.sh/protos/...`) is only
the default for direct generation in this repo.

## Versioning and releases

The wire contract is versioned by the proto package (`v1`) and by git tags
(`vX.Y.Z`) on this repo. `buf breaking` (FILE rules) guards against
incompatible changes.

CI (`.github/workflows/buf.yml`, using `bufbuild/buf-action`) lints, format-checks,
and runs breaking-change detection on every push and PR, and **pushes the module
to the Buf Schema Registry on commits to `main` and on `v*` tags** so other SDKs
can depend on a pinned version. It requires a `BUF_TOKEN` repository secret with
write access to the BSR module. To publish manually:

```bash
buf push   # requires the `name` in buf.yaml to match your BSR org
```

## Consumers

| Consumer | How it uses these protos |
|----------|--------------------------|
| `atol-sdk-go` | Generates Go + Connect-go stubs into its own `gen/` |
| `atol-sdk-js` | Consumes via BSR / generates TS bindings |
| control plane | Generates server stubs; keeps internal-only protos private |
