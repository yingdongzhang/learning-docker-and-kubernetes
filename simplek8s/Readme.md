# k8s notes

* Virtualbox
* [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/)
* kubectl

---
* Declarative deployments
* Nodes are individual machines (or vm's) that run containers
* Masters are machines (or vm's) with a set of programs to mamange nodes
* K8s didn't build the images - it pulls them from a source
* K8s (the master) decides where to run each container - each node can run a dissimilar set of containers
* To deploy something, we define the desired state of the master with a config file
* The master works constantly to meet the desired state
* Name is used to identify existing object by the master

---
# Pods
* Runs a single set of containers
* Good for one-off dev purpose
* Rarely used directly in production
* Every pod gets its own IP address internal to the VM

## Commands

### Apply config
```
kubectl apply -f client-pod.yaml
kubectl apply -f client-node-port.yaml
```

### Get detailed info of object
`kubectl describe pod client-pod`

### Limitations of the Pod config file
* spec: Forbidden: pod updates may not change fields other than `spec.containers[*].image`, `spec.initContainers[*].image`, `spec.activeDeadlineSeconds` or `spec.tolerations`

# Service
* Watches pods that match the selector and route traffic accordingly

# Deployment
* Runs a set of identical pods (one or more)
* Monitors the state of each pod, updating as necessary
* Good for dev & production
* Contains Pod template

## Commands
### Delete object (imperative)
`kubectl delete -f client-pod.yaml`

### Apply deployment
`kubectl apply -f client-deployment.yaml`

### Restart the deployment rollout
`kubectl rollout restart -f client-deployment.yaml`

---
# Connect docker to docker server running in minikube vm, temporary reconfiguration in the current terminal window
`eval $(minikube docker-env)`
