# Kubernetes Namespaces

## Why do we use namespaces?

Namespaces are used to logically isolate resources and organize objects within a cluster. By default, resources are created in the `default` namespace.

```bash
kubectl get namespace
```

Example output:

```text
NAME                 STATUS   AGE
default              Active   15h
kube-node-lease      Active   15h
kube-public          Active   15h
kube-system          Active   15h
local-path-storage   Active   15h
```

The default Kubernetes system services are created in the `kube-system` namespace.

---

## List resources in a namespace

```bash
kubectl get all --namespace namespace_name
kubectl get all -n namespace_name
```

A service in the same namespace can usually be accessed using its hostname. If the service is in another namespace, you need to use the fully qualified domain name (FQDN).

---

## Create a new namespace

We can create a namespace using a YAML file:

```bash
kubectl apply -f namespace.yaml
```

Example YAML:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: demo
```

Then check it:

```bash
kubectl get namespaces
```

You can also create it directly:

```bash
kubectl create ns namespace_name
```

---

## Check pods in a namespace

```bash
kubectl get pods -n demo
```

---

## Connectivity between namespaces

To test communication between two namespaces, you can exec into a pod and test connectivity from one namespace to another. For example, from the `demo` namespace to the `default` namespace.

```bash
kubectl get pods -o wide
kubectl get pods -o wide -n demo
```

When using curl from inside a pod, using the IP address works directly, but using the service name alone may fail because DNS must resolve the fully qualified service name.

---

## Scaling and service exposure

```bash
kubectl scale --replicas=3 deployment nginx-deploy -n demo
kubectl expose deployment nginx-deploy --name=svc-demo --port=80 -n demo
```

Check the service:

```bash
kubectl get svc
```

Example output:

```text
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
cluster-svc   ClusterIP   10.96.230.193   <none>        80/TCP         16h
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP        16h
nginx-svc     NodePort    10.96.25.170    <none>        80:30001/TCP   16h
svc-demo      ClusterIP   10.96.151.126   <none>        80/TCP         32s
```

If you run:

```bash
curl svc-demo
```

it can fail because the service name alone is not resolvable in all cases.

The DNS search path in the pod is usually:

```text
search demo.svc.cluster.local svc.cluster.local cluster.local
nameserver 10.96.0.10
options ndots:5
```

So the correct service reference is:

```bash
curl svc-demo.default.svc.cluster.local
```

This works because it includes the full DNS name.

---

## Summary

Namespaces help separate workloads, teams, and environments logically while sharing the same underlying cluster resources. They are essential for organization, isolation, and multi-environment deployments.
