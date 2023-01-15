# Configuring ingress

- Ingres is provided by the kubernetes ecosystem.
- As a result, vanilla kubernetes doesn't come with ingress controller.
- To use ingress you need to isnstall your own.
- Common ingress controllers solution are Traefik and nginx-ingres.
- nginx-ingres is easier to configure.

## Configure nginx-ingress on vanilla kubernetes

#### Install ingress-nginx on minikube

To use helm we need to install it. Here is an example installing helm on debian and ubuntu.

```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

```
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx   --namespace ingress-nginx --create-namespace
```

#### Ver los pods den namespace de ingress-nginx

```
kubectl get pods -n ingress-nginx
```

#### Create a deploy

```
kubectl create deploy nginxsvc --image=nginx --port=80
```

#### Expose deploy

```
kubectl expose deploy nginxsvc
```

#### Change type from ClusterIP to NodePort

```
kubectl edit svc nginxsvc
```


## Creating ingress resources

#### Create ingress resource

```
kubectl create ingress nginxsvc --class=nginx --rule=nginxsvc.info/*=nginxsvc:80
```

#### forward a local port to the ingress controller

```
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8080:80
```

#### Fake DNS sever
```
sh -c 'echo "127.0.0.1 nginxsvc.info" >> /etc/hosts'
```

#### Test that is accessible

```
curl nginxsvc.info:8080
```