# k8s notes

* Virtualbox
* [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/)

```
kubectl apply -f client-pod.yaml
kubectl apply -f client-node-port.yaml
```

* Declarative deployments
* Nodes are individual machines (or vm's) that run containers
* Masters are machines (or vm's) with a set of programs to mamange nodes
* K8s didn't build the images - it pulls them from a source
* K8s (the master) decides where to run each container - each node can run a dissimilar set of containers
* To deploy something, we define the desired state of the master with a config file
* The master works constantly to meet the desired state
* Name is used to identify existing object by the master

