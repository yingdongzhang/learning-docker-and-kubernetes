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
