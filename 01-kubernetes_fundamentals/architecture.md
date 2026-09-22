# Kubernetes Architecture

## 1. Kubernetes has two main components

1. The control plane, which acts as the brain of the cluster.
2. The worker node, where the application workloads run.

---

## 2. Components of the control plane

### API Server

The API server is the main entry point for cluster communication. It validates requests, checks authentication and authorization, and exposes the Kubernetes API to tools like kubectl.

### etcd

etcd is the cluster database. It stores the desired state and critical cluster information as key-value data, such as nodes, deployments, services, and configuration.

### Controller Manager

The controller manager runs the controllers that monitor the cluster and reconcile the actual state with the desired state.

### Scheduler

The scheduler decides which worker node should run a new pod based on resource availability, constraints, and other scheduling criteria.

---

## 3. Components of a worker node

### Kubelet

The kubelet is the node agent. It communicates with the control plane and ensures that the containers in the pods on that node are running as expected.

### kube-proxy

kube-proxy manages networking rules and service routing on the node.

### Pod

A pod is the smallest deployable unit in Kubernetes. It can contain one or more containers, but usually one container runs per pod.

### Container Runtime

The container runtime is responsible for running containers. Common runtimes include containerd and CRI-O.

### CNI (Container Network Interface)

CNI manages networking between pods and nodes. Tools such as Flannel or Calico are commonly used.

---

## 4. Worker node flow

```text
Worker Node
├── kubelet
├── Container Runtime
│   └── containerd → runc
└── CNI
    └── Flannel
```

```text
Kubelet
   ↓
CRI (Container Runtime Interface)
   ↓
containerd / CRI-O
   ↓
runc
   ↓
Linux Kernel
   ↓
Container
```

---

## 5. What is a pod?

A pod is the smallest unit that can be created and deployed in Kubernetes. A pod may contain one or more tightly coupled containers, but in most cases it runs a single container.

---

## 6. Why does Kubernetes use pods?

Kubernetes uses pods because they represent the smallest deployable unit. Pods allow a group of containers to be treated as a single application unit that shares the same network namespace and lifecycle.

---

## 7. How does kubectl apply work internally?

1. kubectl reads the YAML file and validates the structure.
2. kubectl checks the object kind, name, API version, and metadata.
3. It sends the request to the API server.
4. The API server authenticates, authorizes, and validates the request.
5. The API server stores the desired state in etcd.
6. The controller detects the change and reconciles the difference.
7. The scheduler places the pod on an appropriate node.
8. kubelet instructs the container runtime to create the pod.
9. CNI sets up networking if required.
10. The client receives a success response.

---

## 8. Flow for updating a Deployment from 1 pod to 3 pods

1. A user runs kubectl to send the update request to the API server.
2. The API server authenticates, authorizes, and validates the request.
3. The API server updates the desired state in etcd.
4. The controller manager detects the change through its controllers.
5. The ReplicaSet controller creates the additional pod objects.
6. The scheduler assigns each new pod to a worker node.
7. The kubelet on the selected node creates the pod using the container runtime.
8. The API server updates etcd with the new pod status.
9. The API server returns the response to the client.

---

## 9. How does etcd store data and why?

etcd stores data as key-value pairs. This works well for Kubernetes because different resources have different schemas, and the cluster needs flexibility without relying on a rigid fixed database schema.

---

## 10. How does Kubernetes maintain desired state?

The control plane maintains the desired state. The actual desired configuration is stored in etcd, while controllers continuously compare the live state to the intended state and correct any differences.

---

## 11. Why doesn’t Kubernetes run containers directly?

Kubernetes avoids tightly coupling itself to a specific runtime. By abstracting the runtime through the CRI, Kubernetes can work with multiple container runtimes such as containerd and CRI-O.

---

## 12. How do containers in the same pod communicate?

Containers within the same pod share the same network namespace, so they can communicate using localhost and different ports.

---

## 13. What happens if a pod dies?

1. If a pod fails unexpectedly, kubelet and the runtime try to recreate it.
2. If a pod is removed intentionally during scale-in or rollout, the controller decides which pod should be terminated.

Note: During scaling down, the Deployment's ReplicaSet chooses the pod to remove, and the scheduler is not involved in that decision.

---

## 14. What happens if etcd goes down?

The existing workloads may continue to run, but the cluster loses the source of truth for its desired state. Kubernetes may not know which resources to create, delete, or reschedule, and reconciliation becomes unreliable.

---

## 15. What happens if the controller manager goes down?

Existing workloads may still work, but the cluster may fall out of sync because the controllers are not reconciling the actual state with the desired state.

---

## 16. What happens if kubelet goes down?

If the kubelet on a node goes down, the node can no longer report status correctly, and new or restarted pods on that node may fail to start or recover.

---

## 17. What happens if kube-proxy goes down?

The service routing rules may not be updated correctly. Existing networking rules may continue to function for some time, but the cluster loses healthy service-to-pod communication updates.

---

## 18. Who starts the container?

The container is started by the runtime, which is controlled by the kubelet. The flow is:

kubelet → CRI → containerd or CRI-O → runc

This is how the actual container process is created inside the node.
