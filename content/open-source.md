---
title: "Open Source"
date: 2025-06-18
lastmod: 2026-11-14
draft: false
description: "My contributions to open source projects"
---

Most of my open source work is around Flux and Kubernetes, which I also work with at RBC.

---

## flux9s

[flux9s](https://github.com/dgunzy/flux9s) is a terminal UI for Flux that I maintain, inspired by [k9s](https://github.com/derailed/k9s). It's written in Rust using [ratatui](https://github.com/ratatui-org/ratatui) and [kube-rs](https://github.com/kube-rs/kube).

It reached 1.0 in 2026. It uses Kubernetes watches for live updates and starts in read-only mode. You have to enable writes before changing anything in the cluster.

I wrote about getting to 1.0 on the [Flux blog](https://fluxcd.io/blog/2026/10/flux9s-ga/).

### Recent work

- **Application views:** show a Flux object's sources, inventory, workloads, and pods in a live tree. [Change #326](https://github.com/dgunzy/flux9s/pull/326).
- **Mixed resource views:** put Flux and Kubernetes resources in the same list, with namespace filtering. [Change #324](https://github.com/dgunzy/flux9s/pull/324).
- **Pod and node views:** live CPU and memory usage, clearer allocation information, and navigation from nodes to their pods. [Change #330](https://github.com/dgunzy/flux9s/pull/330).
- **TLS:** switched to rustls so the release binaries no longer need a matching system OpenSSL installation. [Change #334](https://github.com/dgunzy/flux9s/pull/334).

Installation instructions are at [flux9s.ca](https://flux9s.ca/), and the [changelog](https://github.com/dgunzy/flux9s/blob/main/CHANGELOG.md) lists the other changes.

---

## Flux (fluxcd/flux2 / fluxcd/pkg)

Flux is the GitOps toolkit for Kubernetes that powers continuous delivery at RBC and across the broader cloud native ecosystem. My contributions span the CLI and core libraries: adding the `export source external` and `get source external-artifact` commands, promoting the image CLI commands to stable, fixing error reporting in `get.go` to return non-zero exit codes on failure, and a CABundle validation fix in `fluxcd/pkg` to handle Kubernetes 1.31's stricter CRD webhook cert validation.

I also fixed manifest scanning to respect `--ignore-paths` in the shared libraries. [Browse my Flux library commits](https://github.com/fluxcd/pkg/commits/main/?author=dgunzy).

**Project Links:** [flux2](https://github.com/fluxcd/flux2) · [pkg](https://github.com/fluxcd/pkg) · [kustomize-controller](https://github.com/fluxcd/kustomize-controller)

---

## Flux Operator

The Flux Operator manages the lifecycle of Flux installations on Kubernetes and extends Flux with higher-level abstractions like ResourceSets. My contributions include adding **Gitea/Forgejo ResourceSetInputProvider** support — enabling ephemeral preview environments to be provisioned and torn down automatically as part of a GitOps workflow — as well as an **ExternalService input provider** type and OCI insecure registry support for ResourceSetInputProviders.

I also fixed the reconcile-disabled annotation so it is respected when set on a live object, not just in the template. [Browse my Flux Operator commits](https://github.com/controlplaneio-fluxcd/flux-operator/commits/main/?author=dgunzy).

I wrote about using ExternalService with an internal API [here](/posts/flux-externalservice-resourceset/).

**Project Link:** [Flux Operator on GitHub](https://github.com/controlplaneio-fluxcd/flux-operator)

---

## Docker

Docker fundamentally changed how we build and ship software. An early contribution that got me started thinking about open source as something I could actually participate in.

**Project Link:** [Docker on GitHub](https://github.com/docker/docker)

---

## Firestoned

I've also contributed to projects in the [Firestoned organisation](https://github.com/firestoned/).

### banlieue

[banlieue](https://github.com/firestoned/banlieue) manages virtual machines through Kubernetes resources. I added a check to block VM creation when another banlieue VM already claims the same static IP on the same network class ([#72](https://github.com/firestoned/banlieue/pull/72)). I've also worked on the bootstrap scripts and setup documentation ([#189](https://github.com/firestoned/banlieue/pull/189)), and contributed a design document for passing Cluster API machine bootstrap data through to provider machines ([#192](https://github.com/firestoned/banlieue/pull/192)).

### bindy

[bindy](https://github.com/firestoned/bindy) is a Kubernetes operator for BIND9 DNS. I added the `bindy version` command and `--version` support ([#432](https://github.com/firestoned/bindy/pull/432)), and fixed the CLI installation instructions to include extracting the release archive ([#211](https://github.com/firestoned/bindy/pull/211)).

### firestone

[firestone](https://github.com/firestoned/firestone) is a Python project I've contributed to, with a reverse proxy and code generation based on resource definitions.

---

## kube-rs

[kube-rs](https://github.com/kube-rs/kube) is the Rust Kubernetes client I use in flux9s. I've made one contribution so far: fixing the OpenSSL client to use the kubeconfig's `tls-server-name` setting ([#1993](https://github.com/kube-rs/kube/pull/1993)).

---

## Why Open Source

The tools I contribute to are the same ones I rely on at work every day. Contributing back — even in small ways — feels like the right thing to do, and it's one of the best ways I've found to go deeper on a codebase, understand design decisions, and learn from maintainers who really know their stuff.
