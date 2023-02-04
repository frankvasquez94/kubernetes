# StatefulSet

- StatefulSet is used when one of the following is required:
    - Stable unique network identifiers.
    - Stable, persistence storage.
    - Ordered, graceful deployment and scaling.
    - Ordered and automated roling updates.
- Storage must be provided by a PersistenceVolume.
- Deleting a StatefulSet does not delete associated volumes.
- StatefulSet require a headless service.
- It may be required to scale down the StatefulSet to 0 prior to deletion.

A very good use case is running a database instance.


## Demo

1. Create a persistent volume.

Create a file pv.yaml


```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume
  labels:
    type: local
spec:
  storageClassName: my-storage-class
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mydata"
```

```
kubectl create -f pv.yaml
```

```
kubectl get pv
```

2. Create a StatefulSet

Create a file statefulset.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # has to match .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # by default is 1
  minReadySeconds: 10 # by default is 0
  template:
    metadata:
      labels:
        app: nginx # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi
```

```
kubectl create -f statefulset.yaml
```

```
kubectl get statefulset, pod
```

3. Check the pods

```
kubectl get pods
```

```
root@controlplane:~$ kubectl get pod
NAME    READY   STATUS    RESTARTS   AGE
web-0   1/1     Running   0          28m
web-1   0/1     Pending   0          28m
```

So we have the StatefulSet available right here. And why do we have this second pod in a state of pending?,Well that is because I only have one PV and this one PV is currently bound. And we need to repeat this procedure for every single bolt in a StatefulSet. That's how you can work with a StatefulSet.