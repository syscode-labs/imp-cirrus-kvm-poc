# imp-cirrus-kvm-poc

Bridge repo for Cirrus jobs that need KVM-capable public runners.

## Tasks

- `Firecracker VM Command Probe (KVM)`
  - Validates `/dev/kvm` access.
  - Boots a minimal Firecracker VM and verifies a guest command marker.

- `Runtime-Real Template (Talos + ext + imp e2e)`
  - Env-gated (`RUN_RUNTIME_REAL=true`).
  - Clones `imp` and `talos-ext-firecracker` at refs passed via env.
  - Delegates Talos+extension provisioning and runtime-real e2e execution to scripts in `talos-ext-firecracker` (source of truth).

## Runtime Template Contract

The extension repo must provide executable scripts:

- `scripts/ci/provision-talos-with-extension.sh`
- `scripts/ci/run-imp-e2e-runtime-real.sh`

Inputs provided by this repo:

- `IMP_REPO_DIR=/tmp/imp`
- `E2E_LABEL_FILTER` (defaults to `runtime-real`)

## Triggering Runtime Template

Set environment variables when dispatching the Cirrus build:

- `RUN_RUNTIME_REAL=true`
- `IMP_REF=<imp commit or ref>`
- `EXT_REF=<talos-ext-firecracker commit or ref>`
- optional: `E2E_LABEL_FILTER=runtime-real`
