---
title: "Open Source"
date: 2025-06-18
draft: false
description: "My contributions to open source projects"
---

# Open Source Contributions

I'm passionate about contributing back to the open source community that has provided so many amazing tools and technologies. My contributions are primarily in the Flux and Kubernetes ecosystem — tools I work with every day at RBC.

---

## flux9s

flux9s is my own open source project — a terminal UI (TUI) for monitoring FluxCD GitOps resources, inspired by [k9s](https://github.com/derailed/k9s). Built in Rust using [ratatui](https://github.com/ratatui-org/ratatui) and [kube-rs](https://github.com/kube-rs/kube), it gives platform engineers and SREs a fast, keyboard-driven interface for observing Flux resources across a Kubernetes cluster.

Features include real-time resource status, k9s-style navigation, support for all core Flux resource types, and cross-platform distribution via Homebrew and GitHub Releases.

**Project Link:** [flux9s on GitHub](https://github.com/dgunzy/flux9s)
**Website:** [flux9s.ca](https://flux9s.ca)

---

## Flux (fluxcd/flux2 / fluxcd/pkg)

Flux is the GitOps toolkit for Kubernetes that powers continuous delivery at RBC and across the broader cloud native ecosystem. My contributions span the CLI and core libraries: adding the `export source external` and `get source external-artifact` commands, promoting the image CLI commands to stable, fixing error reporting in `get.go` to return non-zero exit codes on failure, and a CABundle validation fix in `fluxcd/pkg` to handle Kubernetes 1.31's stricter CRD webhook cert validation.

**Project Links:** [flux2](https://github.com/fluxcd/flux2) · [pkg](https://github.com/fluxcd/pkg) · [kustomize-controller](https://github.com/fluxcd/kustomize-controller)

---

## Flux Operator

The Flux Operator manages the lifecycle of Flux installations on Kubernetes and extends Flux with higher-level abstractions like ResourceSets. My contributions include adding **Gitea/Forgejo ResourceSetInputProvider** support — enabling ephemeral preview environments to be provisioned and torn down automatically as part of a GitOps workflow — as well as an **ExternalService input provider** type and OCI insecure registry support for ResourceSetInputProviders.

**Project Link:** [Flux Operator on GitHub](https://github.com/controlplaneio-fluxcd/flux-operator)

---

## Docker

Docker fundamentally changed how we build and ship software. An early contribution that got me started thinking about open source as something I could actually participate in.

**Project Link:** [Docker on GitHub](https://github.com/docker/docker)

---

## Firestoned

An open source Python project featuring a reverse proxy and a resource-first code generation approach.

**Project Link:** [Firestoned on GitHub](https://github.com/firestoned/firestone)

---

## Why Open Source

The tools I contribute to are the same ones I rely on at work every day. Contributing back — even in small ways — feels like the right thing to do, and it's one of the best ways I've found to go deeper on a codebase, understand design decisions, and learn from maintainers who really know their stuff.
