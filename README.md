# strata-os

> A cloud-native desktop operating system built on [bootc](https://github.com/bootc-dev/bootc) that reimagines the Linux desktop experience for modern computing environments.

[![Build](https://github.com/eiwanski/strata-os/actions/workflows/build.yml/badge.svg)](https://github.com/eiwanski/strata-os/actions/workflows/build.yml)
[![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-blue.svg)](LICENSE)
[![bootc](https://img.shields.io/badge/built%20on-bootc-orange?logo=fedora&logoColor=white)](https://bootc.dev)
[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/strata-os)](https://artifacthub.io)

## Overview

**strata-os** is a customization of [Bluefin](https://projectbluefin.io/) — a container-oriented, immutable desktop OS by [Universal Blue](https://universal-blue.org/). It provides:

- **Immutable root filesystem** — reliable updates, rollbacks, and atomic deployments
- **Container-native workflows** — development and runtime via Podman
- **Disk image support** — QCOW2, RAW, and ISO builds via [Bootc Image Builder](https://github.com/osbuild/bootc-image-builder)
- **OCI signing** — verified image integrity with [cosign](https://github.com/sigstore/cosign)

## Quick Start

### Prerequisites

- A [bootc](https://bootc.dev)-based host (Bazzite, Bluefin, Aurora, or Fedora Atomic)
- [just](https://just.systems/) — task runner
- [podman](https://podman.io/) — container runtime
- A GitHub account (for image publishing)

### Build & Deploy

```bash
# 1. Configure your image settings
cp image-template.env .env   # adjust IMAGE_NAME, REPO_ORGANIZATION, etc.

# 2. Build the container image
just build

# 3. Switch to the new image
sudo bootc switch ghcr.io/eiwanski/strata-os:latest

# 4. Reboot
sudo reboot
```

### Build Disk Images

```bash
# QCOW2 (QEMU/KVM)
just build-qcow2

# RAW
just build-raw

# ISO
just build-iso
```

Run and test with:

```bash
just run-vm-qcow2    # launches via QEMU with web console
just spawn-vm        # launches via systemd-vmspawn
```

## Repository Structure

```
├── Containerfile          # Image definition (based on Bluefin-DX)
├── Justfile               # Build, VM, and utility tasks
├── build.sh               # Package installation & customization
├── image-template.env     # Image configuration variables
├── build_files/           # Build scripts mounted at build time
├── system_files/          # System configuration files
├── disk_config/           # Disk image builder configs (TOML)
├── artifacthub-repo.yml   # Artifact Hub indexing metadata
├── cosign.pub             # Public signing key
└── .github/workflows/     # CI: build, signing, disk images
```

## Key Files

| File | Purpose |
|---|---|
| [Containerfile](./Containerfile) | Defines the base image and customization layers |
| [build.sh](./build_files/build.sh) | Install packages, apply configs, customize the image |
| [Justfile](./Justfile) | All build, test, and VM commands (run `just` to list) |
| [image-template.env](./image-template.env) | Environment variables for image name, tags, and metadata |
| [build.yml](./.github/workflows/build.yml) | GitHub Actions workflow for OCI image builds |
| [build-disk.yml](./.github/workflows/build-disk.yml) | GitHub Actions workflow for disk image generation |

## Available Just Commands

Run `just` to see all recipes. Key commands:

| Category | Command | Description |
|---|---|---|
| **Build** | `just build` | Build the OCI container image |
| | `just rechunk` | Rechunk layers with chunkah |
| | `just ostree-rechunk` | Rechunk layers with rpm-ostree |
| **Disk Images** | `just build-qcow2` | Build a QCOW2 VM image |
| | `just build-raw` | Build a RAW VM image |
| | `just build-iso` | Build an ISO image |
| **VM** | `just run-vm-qcow2` | Run a QCOW2 VM with web console |
| | `just spawn-vm` | Run a VM via systemd-vmspawn |
| **Utility** | `just check` | Validate Justfile syntax |
| | `just fix` | Auto-format Justfile |
| | `just lint` | Run shellcheck on all scripts |
| | `just format` | Run shfmt on all scripts |
| | `just clean` | Remove build artifacts |

## Base Image

This image is based on **Bluefin-DX** (`ghcr.io/ublue-os/bluefin-dx:stable`). Other Universal Blue bases are available:

| Image | Tag |
|---|---|
| Bazzite (gaming) | `ghcr.io/ublue-os/bazzite:stable` |
| Aurora (KDE) | `ghcr.io/ublue-os/aurora:stable` |
| Bluefin (GNOME) | `ghcr.io/ublue-os/bluefin:stable` |
| Fedora Atomic | `quay.io/fedora/fedora-bootc:44` |

Change the `FROM` line in [Containerfile](./Containerfile) to switch bases.

## Customization

Customize your image by editing [build.sh](./build_files/build.sh). This script runs during the container build and is the primary place to:

- Install additional packages
- Apply system configurations from [system_files/](./system_files/)
- Configure services and users

## Signing

Images are signed with [cosign](https://github.com/sigstore/cosign). Set up your signing key:

```bash
COSIGN_PASSWORD="" cosign generate-key-pair
```

Upload `cosign.key` as a GitHub secret named `SIGNING_SECRET`. The `cosign.pub` public key is committed to the repo for verification.

## Artifact Hub

This image is indexed on [Artifact Hub](https://artifacthub.io). Update [artifacthub-repo.yml](./artifacthub-repo.yml) with your repository ID and owner details to claim and manage your listing.

## Community

For help and discussions:

- [Universal Blue Forums](https://universal-blue.discourse.group/)
- [Universal Blue Discord](https://discord.gg/WEu6BdFEtp)
- [bootc Discussions](https://github.com/bootc-dev/bootc/discussions)
- [Bluefin Documentation](https://docs.projectbluefin.io/)
- [Bluefin GitHub](https://github.com/ublue-os/bluefin/)

## License

This project is licensed under the [Apache License 2.0](LICENSE).

---

*Built with ❤️ on bootc — an immutable, container-native operating system.*
