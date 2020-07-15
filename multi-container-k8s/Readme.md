# ClusterIP
Exposes a set of pods to other objects in the cluster

# Volume

## "Volume" in k8s
An object that allows a container to store data at the pod level. If pod goes away the volume is gone.

## Persistent Volume
Long time durable storage that is outside of the pod.

## Persistent Volume Claim
Not created ahead of time but when asked for

```
kubectl get storageclass
kubectl describe storageclass
kubectl get pv
kubectl get pvc
```

# Secrets
Securely stores a piece of information in the cluster, such as a database password.
We use imperative approach to define the secret.

`kubectl create secret generic <secret_name> --from-literal key=value`

Type of secrets:
* generic
* docker-registry
* tls

# Load Balancer
Legacy way of getting network traffic into a cluster. Sits in front of a set of pods within a deployment

# Ingress Service
Exposes a set of services to the outside world
* ingress-nginx: a project led by k8s community
* kubernetes-ingress: a project led by Nginx company

## Ingress config
Object that has a set of configuration rules describing how traffic should be routed

## Ingress Controller
Watches for changes to the ingress and updates the 'thing' that handles traffic

# Minikube dashboard
`minikube dashboard`