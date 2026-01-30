# k8s-argocd-helm-demo

## Requirements

```
Docker
Kubectl
```

## Steps

### Setup Minikube

```
brew install minikube
```

Once is installed, be sure to have docker running. You can check if your local `PATH` env is correct running

```
minikube version
```

You should see the version running, something like this `minikube version: v1.37.0`

It is time to start the cluster

```
minikube start
```

It may take a while downloading things and setting up the container to run minikube.

You can also see the cluster info running 

```
kubectl cluster-info
```

### Setup ArgoCD

Documentation https://argo-cd.readthedocs.io/en/stable/

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

To validate the resources created you can run this

```
kubectl get pods -n argocd
```

Now we need to expose the service using a load balancer

```
kubectl patch svc argocd-server -n argocd -p '{"spec" : {"type" : "LoadBalancer"}}'
```

Running `kubectl get svc -n argocd` you should be able to see the new LoadBalancer created

Now we must assign an external IP address to the service. You can retrieve this IP with:

```
kubectl get svc argocd-server -n argocd -o=jsonpath='{.status.loadBalancer.ingress[0].ip}'
````

And also, forward the port to localhost access with:

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Now you should be able to access the UI, but firts we need to retrieve the admin password for our ArgoCD installation. You can retrieve it running this:

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
