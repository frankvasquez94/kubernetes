# DaemonSet

- Kubernetes offers different resources to run applications.
- The deployment is the default resource, used to schedule replicated applications.
- The DaemonSet is used to run one instance of an application on every node in the cluster.
- The StatefulSet is used for stafull applications and provides guarantees about ordering and uniqueness of its pod.
- You'll find DaemonSet already used for agents on you cluster.
- Use **kubectl get ds -A** for an overview.
- Otherwise is relatively easy to build a DaemonSet based on a deployment.
- Notice because on taints, Pods may not be started on the control plane.

## Demo

1. Creat a deployment.
```
kubectl create deploy daemonnginx --image=nginx --dry-run=client -o yaml > daemonnginx.yaml
```
2. Edit the daemonnginx.yaml
-- Change **kind: Deployment** to **kind: DaemonSet**
-- remove **replicas**
-- Remove **status**
-- Remove **strategy**
3. Create the DaemonSet

```
kubectl create -f daemonnginx.yaml
```