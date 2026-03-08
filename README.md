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

## Configuration-First Build Flow

This repo uses tool-native configuration (not ad-hoc orchestration scripts) for base image build/publish:

- Image definition: `ci/images/runtime-base/Dockerfile`
- Publish workflow: `.github/workflows/publish-runtime-base-image.yml`
- Runtime consumption in Cirrus: `.cirrus.yml`

Publishing is done with official Docker GitHub Actions:

- `docker/setup-buildx-action`
- `docker/login-action`
- `docker/metadata-action`
- `docker/build-push-action`

## Base Image Cache

Cirrus tasks use a prebuilt GHCR image to avoid repeated package installs:

- `ghcr.io/syscode-labs/imp-cirrus-runtime-base:latest`

The publish workflow pushes:

- `latest` (default branch)
- `sha-<commit>`

## Updating the Base Image

1. Edit `ci/images/runtime-base/Dockerfile`
2. Push to `main` (or trigger `Publish Runtime Base Image` manually)
3. Wait for publish workflow success in GitHub Actions
4. Cirrus tasks on new commits pull updated `latest`

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
