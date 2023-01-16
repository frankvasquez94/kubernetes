# Canary deployment

- Canary deployment is an upgrade strategy.
- The essense is that a limited number of application instances is presented to the user before completing the upgrade.
- The adventage is that, if it does not turn out well, the upgrade easily can be undone, and only a limited percentage of users is affected.
- The essence in canary deployment is the service resource uses a selector label to connect to the old version as well as the new version.

## Creating a first version

#### Create oldnginx configMap
```
echo oldsite" > oldnginx.html
```

```
kubectl create cm oldnginx --from-file=oldnginx.html
```

Get config maps

```
kubectl get cm
```

#### Create oldnginx deployment

```
kubectl create deploy oldnginx --image=nginx:1.14 --replicas=3 --dry-run=client -o yaml > oldnginx.yaml
```

We need to edit deplyment to configure label and mount config file

```
vim oldnginx.yaml
```

Add config map olgnginx and label to canary. See oldnginx.yaml.


- add label canary
- add option to mount configMap
- Mount configMap

The last step is creating the deploy

```
kubectl create -f oldnginx.yaml
```

#### Expose deployment

```
kubectl expose deploy oldnginx --name=canary --port=80 --selector typed=canary
```

#### Change the type to NodePort the name has been set to canary for oldnginx
```
kubectl edit svc canary
```

#### Creating a second version
In this case we can copy everything about the first version and change from old to new.
Change replicas=1
Change image=nginx

In canary deployment we start with few replicas and the we scale or undone the version as well.

```
cp oldnginx.yaml newnginx.yaml
```

Replace old string for new by using vim
```
:%s/old/new/
```


#### Create newnginx configMap
```
echo newsite" > newnginx.html
```

```
kubectl create cm newnginx --from-file=newnginx.html
```

Get config maps

```
kubectl get cm
```

#### Create newnginx deployment

```
kubectl create deploy newnginx --image=nginx --replicas=1 --dry-run=client -o yaml > newnginx.yaml
```

We need to edit deplyment to configure label and mount config file

```
vim newnginx.yaml
```

Add config map newnginx and label to canary. See newnginx.yaml.


- add label canary
- add option to mount configMap
- Mount configMap

The last step is creating the deploy

```
kubectl create -f newnginx.yaml
```

#### Change the type to NodePort the name has been set to canary for oldnginx

```
kubectl edit svc canary
```

## Testing the canary deployment

#### Describe the canary configurations
```
kubectl describe svc canary
```

#### First testing

Repeat at least 8 times
We will get forbidden because it's a problem in configuration maps
```
curl nodeIp:ServiceNodePortPort
```

We need to change it because apache server is looking not for newnginx.html and oldnginx.html, it's looking for index.html

#### Editing configMaps

Change oldnginx.html by index.html

```
kubectl edit cm oldnginx
```

Change newnginx.html by index.html

```
kubectl edit cm newnginx
```

#### Scaling newnginx

Now that the update newnginx is running ok, we can scale our newnginx to 3 replicas and delete oldnginx by scaling to 0 replicas.

#### Upgrading newnginx to new version
```
kubectl scale deploy newnginx --replicas=3
```

#### Deleting old version

```
kubectl scale deploy oldnginx --replicas=0
```