# Namespace Interview Questions

## Q: What is a Kubernetes namespace?

A namespace is a logical partition inside a Kubernetes cluster. It helps isolate resources and organize them by environment, team, or application.

---

## Q: Why would you use namespaces?

Namespaces are used to separate workloads such as development, staging, and production. They help keep resources organized and reduce accidental conflicts.

---

## Q: Are namespaces like separate Kubernetes clusters?

Not exactly. Namespaces are logical boundaries inside the same cluster. They still share the same control plane and worker nodes, but resources are isolated within their own namespaces.

---

## Q: Can two namespaces have pods with the same name?

Yes. Pod names only need to be unique within a namespace, not across the entire cluster.

---

## Q: Can two namespaces have services with the same name?

Yes. Services can share the same name across namespaces because they are namespaced resources.

---

## Q: How do you list all namespaces?

```bash
kubectl get namespaces
```

---

## Q: How do you list pods in a specific namespace?

```bash
kubectl get pods -n namespace_name
```

---

## Q: How do you make a namespace the default for kubectl context?

```bash
kubectl config set-context --current --namespace=dev
```

---

## Q: What happens if you do not specify `-n`?

Kubectl uses the default namespace configured for the current context.

---

## Q: Which Kubernetes resources are namespace-scoped?

Examples include:

- Pod
- Deployment
- ReplicaSet
- StatefulSet
- DaemonSet
- Service
- ConfigMap
- Secret
- PersistentVolumeClaim
- Role

---

## Q: Which resources are cluster-scoped?

Examples include:

- Node
- PersistentVolume
- StorageClass
- Namespace
- ClusterRole
- ClusterRoleBinding
- CustomResourceDefinition (CRD)

Note: PersistentVolume is cluster-scoped, while PersistentVolumeClaim is namespace-scoped.

---

## Q: Can a service in the `dev` namespace communicate with a pod in the `prod` namespace?

Yes. Services from different namespaces can communicate using their FQDN:

```text
<service-name>.<namespace>.svc.cluster.local
```

Network policies can restrict which namespaces or pods are allowed to communicate.

---

## Q: How does DNS work between namespaces?

The DNS format is:

```text
<service-name>.<namespace>.svc.cluster.local
```

---

## Q: How would you prevent developers in the `dev` namespace from accessing resources in `prod`?

Use RBAC and network policies to restrict access and communication between namespaces.

---

## Q: Can you delete a namespace? What happens to the resources inside it?

Yes. Deleting a namespace removes all resources inside it, including pods, services, deployments, and config objects.

---

## Q: What is the difference between a namespace and a cluster?

A namespace is a logical boundary inside a cluster. A cluster is the entire Kubernetes environment, including control plane and worker nodes.
