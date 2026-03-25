---
title: "API-Driven GitOps with Flux Operator's ExternalService"
date: 2026-03-24
draft: false
description: "How ResourceSets and ExternalService input providers flip the GitOps model from push to pull — and why that matters at scale."
tags: ["flux", "kubernetes", "gitops", "platform-engineering"]
---

One of the things I have been occupied with lately is figuring out how to scale GitOps past the point where managing a Git repo, or many nested manifests per cluster, becomes unworkable. The answer we landed on is Flux Operator's **ResourceSet** and **ResourceSetInputProvider** — specifically the `ExternalService` input type, which I helped contribute upstream. (There was already an issue open for exactly this when I looked!)

The full writeup with working examples lives at [dgunzy.github.io/flux-resourceset](https://dgunzy.github.io/flux-resourceset/), but here's the short version.

## The problem with traditional GitOps at scale

Standard Flux works great: point it at a Git repo, it reconciles your cluster. But once you're managing dozens or hundreds of clusters, the model starts to creak:

- Every cluster change means a PR, a review, a merge, a rollout
- Small typos or invalid YAML means a huge delay
- Configuration drift creeps in between clusters
- Your Git repo becomes an inventory file that's always slightly behind, or ahead of reality
- A separate system (CMDB, Ansible inventory, internal API) is the _actual_ source of truth — you're just manually syncing to Git

## The pull-based alternative

Flux Operator's ResourceSet flips this. Instead of pushing config into Git for each cluster, each cluster pulls its desired state directly from your API on a schedule.

{{< mermaid >}}
flowchart LR
U([User]) -->|config change| API[Internal API]
API --> BL[Business Logic]
BL --> DB[(Source of Truth)]

    DB -->|poll| RSIP_A[InputProvider] --> RS_A[ResourceSet] --> CL_A[cluster-a]
    DB -->|poll| RSIP_B[InputProvider] --> RS_B[ResourceSet] --> CL_B[cluster-b]
    DB -->|poll| RSIP_C[InputProvider] --> RS_C[ResourceSet] --> CL_C[cluster-c]

{{< /mermaid >}}

The two key pieces are:

**ResourceSet** — a Flux-native CRD that renders Kubernetes manifests from templated data. Think of it like Helm but without the chart overhead, and with built-in inventory tracking and garbage collection. When a cluster is decommissioned, the next reconcile cycle automatically cleans up everything it created.

**ResourceSetInputProvider (ExternalService)** — this is the bit I helped to contribute. It lets a ResourceSet poll an external HTTP API for its input data, rather than reading from a Git repo or an OCI artifact. Your API returns a JSON response in the format `{"inputs": [...]}` and Flux takes it from there.

## What this looks like in practice

Every cluster boots with Flux Operator pre-installed. On first boot, it registers itself and starts polling our internal cluster API. The API response tells it:

- Which platform components to install (KEDA, monitoring stack, ingress controllers, etc.)
- Namespace configuration and RBAC
- Environment-specific overrides (DEV vs PROD policies)

When we need to roll out a new platform component across 50 clusters, we update the API response. No PRs, no per-cluster branches, no release ceremonies. Flux reconciles on its next poll — done. If something is broken, you can roll it back just as easily, and have all the auditing and tracking in your API.

The `ResourceSetInputProvider` config looks roughly like this:

```yaml
apiVersion: fluxcd.controlplane.io/v1
kind: ResourceSetInputProvider
metadata:
  name: cluster-config
spec:
  type: ExternalService
  url: https://internal-api.example.com/api/v1/flux/clusters/my-cluster/platform-components
  interval: 5m
```

Pair that with a ResourceSet that templates against the inputs, and you have a fully automated, API-driven GitOps loop.

## Why this matters

The real win isn't just less manual work — it's eliminating the divergence between your CMDB and your actual running state. With a push-based model, those two things are always slightly out of sync. With this, the API _is_ the source of truth, and the cluster _is_ the API's state made real. No reconciliation lag, no stale inventory.

It also scales horizontally in a way that push-based models don't. Since clusters initiate requests rather than receiving pushes, you can have thousands of clusters without a centralized bottleneck. (And without your API having cluster admin credentials.)

One other benefit which is not unique to this approach is that clusters can be created and torn down with a single command, which also helps in disaster recovery situations.

To be clear, this is not a replacement for having manifests in Git or OCI. It works alongside version-controlled repositories, enabling deployment through ResourceSets and patching only what is needed to distinguish cluster A from cluster B.

---

If you're running Flux Operator 0.43+, ExternalService support is available now. The full example repo with a working API server and ResourceSet templates is at [dgunzy.github.io/flux-resourceset](https://dgunzy.github.io/flux-resourceset/).
