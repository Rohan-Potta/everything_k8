# Kubernetes Basics

## Why do we use Kubernetes?

Kubernetes helps us manage large numbers of containers and microservices efficiently. It automates scaling, healing, updates, and orchestration across many nodes.

For example, if we have 100 containers and one goes down, Kubernetes can restart or replace it automatically. It also helps us roll out application upgrades smoothly.

---

## Is Kubernetes always the right solution?

No. Kubernetes is not always the best choice. It can be expensive and resource-heavy, especially when the number of clusters and nodes is small. For simple applications, a smaller deployment model may be more efficient.

---

## What should the kubectl version be compared to the cluster version?

The best practice is to keep them at the same version, or within a supported minor version skew of about +/- 1.

Example:

```bash
kubectl version
```

Sample output:

```text
Client Version: v1.32.2
Kustomize Version: v5.5.0
Server Version: v1.29.4
WARNING: version difference between client (1.32) and server (1.29) exceeds the supported minor version skew of +/-1
```

This shows both the client and server versions, and highlights if the version skew is unsupported.

---

## What is the latest Kubernetes version?

At the time of writing, Kubernetes v1.37.0 is considered the latest, and the ecosystem typically tracks the last few recent releases.

---

## Summary

Kubernetes is mainly used to orchestrate containerized workloads at scale, automate recovery, and simplify deployment management. It is powerful, but it should be used where its benefits justify the operational overhead.
