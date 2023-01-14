
# Momitoring pods

1. To monitoring pods and node performance, resource usages statistic must be gathered.
2. Metrics server is required to do so
3. Before using metrics server must be installed

# install metrics server

### Intall metrics server

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

### See metrics server pods is running
Metrics server in this case is not running because needs TLS certificate.

```
kubectl get pods -A
```

### Disable TLS from metrics server

```
kubectl edit deploy -n kube-system metrics-server
```

We need to add an argument
    --kubelet-insecure-tls

``` 
spec.containers:
  -args:
    - --kubelet-insecure-tls
```

Metrics server now is running.

### See metrics

``` 
kubectl top pods -A
```




