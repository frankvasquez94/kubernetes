
# Monitoring pods

1. To monitor pods and node performance, resource usages statistic must be gathered.
2. Metrics server is required to do so.
3. Before using metrics server must be installed.

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
```
root@controlplane:~$ kubectl top pod -A
NAMESPACE      NAME                                   CPU(cores)   MEMORY(bytes)   
default        newnginx-75c978677c-4fxql              0m           2Mi             
default        newnginx-75c978677c-hbz6k              0m           2Mi             
default        newnginx-75c978677c-qpqlw              0m           2Mi             
kube-flannel   kube-flannel-ds-m5v6z                  10m          9Mi             
kube-flannel   kube-flannel-ds-srhc8                  11m          9Mi             
kube-system    coredns-787d4945fb-tfbfm               2m           11Mi            
kube-system    coredns-787d4945fb-v6lpc               2m           12Mi            
kube-system    etcd-controlplane                      25m          35Mi            
kube-system    kube-apiserver-controlplane            46m          274Mi           
kube-system    kube-controller-manager-controlplane   18m          43Mi            
kube-system    kube-proxy-7j6c7                       1m           13Mi            
kube-system    kube-proxy-lh6st                       1m           12Mi            
kube-system    kube-scheduler-controlplane            4m           17Mi            
kube-system    metrics-server-c597f4649-qrtxk         4m           17Mi  
```


```
kubectl top node
```

```
root@controlplane:~$ kubectl top node
NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
controlplane   139m         6%     1356Mi          35%       
node01         46m          2%     765Mi           20% 
```

