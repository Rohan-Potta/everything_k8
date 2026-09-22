# JSON in Kubernetes

## What is JSON?

JSON stands for JavaScript Object Notation. It is a lightweight format used to represent structured data.

---

## Where is JSON used in Kubernetes?

JSON is used heavily in the Kubernetes API and in etcd, because etcd stores data as key-value pairs and Kubernetes resources are represented as structured objects.

```text
user -> kubectl -> API server -> response in JSON -> kubectl formats it for humans
```

To view the full JSON response:

```bash
kubectl get nodes -o json
```

This shows the complete object response. The same concept also works with YAML output.

---

## How do we query JSON output?

Kubernetes supports JSONPath to filter object output and extract only the fields we need.

```bash
kubectl get pods -o=jsonpath='{$.items[*].metadata.labels.run}'
```

This prints the labels present on the pods.

Note: In Kubernetes, we usually do not need to explicitly mention `$` in the query in the same way we do in other contexts.

JSONPath is a way to query or filter Kubernetes API output and extract specific fields.

---

## Sorting output

We can sort Kubernetes resources by metadata fields.

```bash
kubectl get pods --sort-by=.metadata.creationTimestamp
```

On PowerShell:

```powershell
kubectl get pods --sort-by=.metadata.creationTimestamp | Sort-Object -Descending
```

Another example:

```bash
kubectl get nodes --sort-by=.status.capacity.cpu
```

---

## Important note

When parsing Kubernetes output, it is best to use the output of `kubectl get` commands rather than assuming the YAML manifest matches the live object exactly.

This is because the actual runtime object may include additional fields such as status, timestamps, and generated metadata.
