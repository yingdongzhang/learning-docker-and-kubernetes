# **k8s learning notes**

1. [Components](#components)
    1. [Scheduler](#scheduler)
    1. [Node](#node)
        1. [Worker Node](#worker-node)
        1. [Master Node](#master-node)
    1. [Replica Sets](#replica-sets)
    1. [Controller](#controller)
    1. [Container Runtime](#container-runtime)
    1. [Cluster](#cluster)
    1. [API Server](#api-server)
    1. [#ectd](#etcd)
    1. [kubelet](#kubelet)
1. [Kubectl](#kubectl)
    1. [Imperative Commands](#imperative-commands)
    1. [Tips](#tip-kubectl)
    1. [Edit Pod](#edit-pod)
    1. [Edit Deployment](#edit-deployment)
1. [Pod](#pod)
1. [Deployment](#deployment)
1. [Service](#service)
1. [Commands and Arguments](#commands-arguments)
1. [Environment Variables](#environment-variables)
    1. [ConfigMap](#configmap)
    1. [SecretKeys](#secret-keys)
1. [Security Contexts](#security-contexts)
    1. [Service Accounts](#service-account)
1. [Resource Requirements and Limites](#resource-requirements-limits)
1. [Taints and Tolerations](#taints-tolerations)
1. [Node Selectors](#node-selectors)
1. [Node Affinity](#node-affinity)
1. [Observability](#observability)
   1. [Monitoring and Debugging](#monitoring-debugging)
1. [Pod Design](#pod-design)
1. [Updates and Rollbacks](#updates-rollbacks)
1. [Jobs](#jobs)
1. [Services](#services)
1. [Networking](#networking)

# <a name="components"></a>
# Components
## <a name="scheduler"></a>
## Scheduler

Distributes work across nodes

## <a name="node"></a>
## Node
### <a name="worker-node"></a>
### Worker node

- Hosts containers

- kubelet

- pod

	- a single instance of an application

	- the smallest unit in the cluster

### <a name="master-node"></a>
### Master node

- kube-apiserver

- etcd

- controller

- scheduler

## <a name="replica-sets"></a>
## ReplicaSets

- Create multiple instances of pods

- Can monitor existing pods

## <a name="controller"></a>
## Replication Controller(older)
## Controller
Brain behind orchestration

## <a name="container-runtime"></a>
## Container Runtime

## <a name="cluster"></a>
## Cluster

A group of nodes

## <a name="api-server"></a>
## API Server

FE/Terminal of the k8s cluster

## <a name="etcd"></a>
## etcd

Distributed across all nodes key-value store to store data

## <a name="kubelet"></a>
## kubelet

Agent on each node, control containers

## <a name="kubectl"></a>
## kubectl

Run container with interactive mode:
`kubectl exec -it <pod-name> -- <command>`

`run <name> --image <image>`

`get nodes`

`cluster-info`

`get pods -o wide`

`create -f <definition.yaml>`

`describe pods/<name>`

`delete pods/<name>`

`get pod <name> -o yaml > pod.yaml`

`edit pod <name>`

`apply -f`

`get all`

# <a name="imperative-commands"></a>
# Tip: Imperative Commands
While you would be working mostly the declarative way - using definition files, imperative commands can help in getting one time tasks done quickly, as well as generate a definition template easily. This would help save a considerable amount of time during your exams.

Before we begin, familiarize with the two options that can come in handy while working with the below commands:

`--dry-run`: By default as soon as the command is run, the resource will be created. If you simply want to test your command, use the `--dry-run=client` option. This will not create the resource, instead, tell you whether the resource can be created and if your command is right.
`-o yaml`: This will output the resource definition in YAML format on the screen.

Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.

# <a name="tip-kubectl"></a>
# Tip: Formatting Output with kubectl
The default output format for all kubectl commands is the human-readable plain-text format.

The -o flag allows us to output the details in several different formats.

`kubectl [command] [TYPE] [NAME] -o <output_format>`

Here are some of the commonly used formats:

`-o json` Output a JSON formatted API object.

`-o name` Print only the resource name and nothing else.

`-o wide` Output in the plain-text format with any additional information.

`-o yaml` Output a YAML formatted API object.

Here are some useful examples:

## Output with JSON format:

```json
master $ kubectl create namespace test-123 --dry-run -o json
{
  "kind": "Namespace",
  "apiVersion": "v1",
  "metadata": {
    "name": "test-123",
      "creationTimestamp": null
  },
  "spec": {},
  "status": {}
}
master $
```

## Output with YAML format:

```yaml
master $ kubectl create namespace test-123 --dry-run -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: test-123
spec: {}
status: {}
```

## Output with wide (additional details):

Probably the most common format used to print additional details about the object:

```bash
master $ kubectl get pods -o wide
NAME      READY   STATUS    RESTARTS   AGE     IP          NODE     NOMINATED NODE   READINESS GATES
busybox   1/1     Running   0          3m39s   10.36.0.2   node01   <none>           <none>
ningx     1/1     Running   0          7m32s   10.44.0.1   node03   <none>           <none>
redis     1/1     Running   0          3m59s   10.36.0.1   node01   <none>           <none>
master $
```

## <a name="edit-pod"></a>
## Edit a POD
Remember, you CANNOT edit specifications of an existing POD other than the below.

* spec.containers[*].image

* spec.initContainers[*].image

* spec.activeDeadlineSeconds

* spec.tolerations

For example you cannot edit the environment variables, service accounts, resource limits (all of which we will discuss later) of a running pod. But if you really want to, you have 2 options:

1. Run the kubectl edit pod <pod name> command.  This will open the pod specification in an editor (vi editor). Then edit the required properties. When you try to save it, you will be denied. This is because you are attempting to edit a field on the pod that is not editable.

A copy of the file with your changes is saved in a temporary location as shown above.

You can then delete the existing pod by running the command:

`kubectl delete pod webapp`

Then create a new pod with your changes using the temporary file

`kubectl create -f /tmp/kubectl-edit-ccvrq.yaml`

2. The second option is to extract the pod definition in YAML format to a file using the command

`kubectl get pod webapp -o yaml > my-new-pod.yaml`

Then make the changes to the exported file using an editor (vi editor). Save the changes

`vi my-new-pod.yaml`

Then delete the existing pod

`kubectl delete pod webapp`

Then create a new pod with the edited file

`kubectl create -f my-new-pod.yaml`

## <a name="edit-deployment"></a>
## Edit Deployments
With Deployments you can easily edit any field/property of the POD template. Since the pod template is a child of the deployment specification,  with every change the deployment will automatically delete and create a new pod with the new changes. So if you are asked to edit a property of a POD part of a deployment you may do that simply by running the command

`kubectl edit deployment my-deployment`


## <a name="pod"></a>
## POD
Create an NGINX Pod

`kubectl run nginx --image=nginx`

Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)

`kubectl run nginx --image=nginx  --dry-run=client -o yaml`

## <a name="deployment"></a>
## Deployment
Create a deployment

`kubectl create deployment --image=nginx nginx`

Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

**IMPORTANT**:

`kubectl create deployment` does not have a `--replicas` option. You could first create it and then scale it using the `kubectl scale` command.

Save it to a file - (If you need to modify or add some other details)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`

You can then update the YAML file with the replicas or any other field before creating the deployment.

## <a name="service"></a>
## Service
Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379

`kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml`

(This will automatically use the pod's labels as selectors)

Or

`kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml` (This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:

`kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml`

(This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

`kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml`

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

# <a name="commands-arguments"></a>
# Commands and Arguments
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  containers:
    - name: ubuntu-sleeper
      image: ubuntu-sleeper
      command: ["sleep"] # -> ENTRYPOIND in Dockerfile
      args: ["10"] # -> CMD in Dockerfile
```

# <a name="environment-variables"></a>
# Environment Variable

## Key value pair
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  containers:
    - name: ubuntu-sleeper
      image: ubuntu-sleeper
      env:
        - name: APP_COLOR
          value: pink
```

## <a name="configmap"></a>
## ConfigMap
1. Create ConfigMap
    1. Imperative: `kubectl create configmap <config-name> --from-literal=<key>=<value>`, `--from-file=<path-to-file>`
    1. Declarative: `kubectl create -f config.yaml`
        ```yaml
        apiVersion: v1
        kind: Config
        metadata:
          name: app-config
        data:
          APP_COLOR: blue
          APP_MODE: prod
        ```
1. Inject into the pod

```yaml
# 1. Using configMapRef
envFrom:
  - configMapRef:
    name: app-config # name of the ConfigMap
```

```yaml
# 2. Inject single value using configMapKeyRef
env:
  - name: APP_COLOR
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_COLOR
```

```yaml
# 3. Inject as file in the volume
volumes:
  - name: app-config-volue
    configMap:
      name: app-config
```

### View ConfigMaps

`kubectl get configmaps`

`kubectl describe configmaps`

## <a name="secret-keys"></a>
## Secret Keys
For storing sensitive data
1. Create Secret
    1. Imperative: `kubectl create secret generic <secret-name> --from-literal=<key>=<value>`, `--from-file=<path-to-file>`
    1. Declarative: `kubectl create -f`
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: app-secret
    data:
      DB_Host: bXlzcWw=
      DB_User: cm9vdA==
      DB_Password: cGFzd3Jk
    ```
    `echo -n 'password' | base64`
1. Inject into pod
```yaml
# 1. Inject using as variables
envFrom:
  - secretRef:
      name: app-secret
```

```yaml
# 2. Inject using as single env variable
env:
  - name: DB_Password
    valueFrom:
      secretKeyRef:
        name: app-secret
        key: DB_Password
```

```yaml
# 3. Inject as volumnes
volumes:
  - name: app-secret-volume
    secret:
      secretName: app-secret
```

### View Secrets

`kubectl get secrets`

`kubectl describe secrets`

# <a name="security-contexts"></a>
# Security Contexts
## Docker Security
Process running in the container can be seen in the host too

### Users
Docker run process within the containers as the root user. The root user within the container runs with limited capabilities.

Can run docker container as a different user, or use the USER command

## K8s Security Contexts
```yaml
# Pod level
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  securityContext:
    runAsUser: 1000
  containers:
    ...
```

```yaml
# Container level
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  containers:
    - name: ubuntu
      image: ubuntu
      command: ["sleep", "5000"]
      securityContext:
        runAsUser: 1000
```

```yaml
# Add capabilities
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-4
spec:
  containers:
    - name: sec-ctx-4
      image: gcr.io/google-samples/node-hello:1.0
      securityContext:
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

## <a name="service-account"></a>
## Service Account
`kubectl create serviceaccount <service-account-name>`

`kubectl get serviceaccount`

`kubectl describe serviceaccount <service-account-name>`

Service accounts are generated with a token, which will be used as an authentication bearer token to interact with k8s api

```yaml
automountServiceAccountToken: false
```

# <a name="resource-requirements-limits"></a>
# Resource Requirements & Limits

* CPU
* MEM
* Disk

```yaml
# pod spec
spec:
  containers:
    - name: my-pod
      resources:
        requests:
          memory: "1Gi" # 256Mi, 1G(Gigabyte), 1Gi(Gibibyte)
          cpu: 1 # 1 count of CPU = 1 AWS vCPU, 1 GCP Core, 1 Azure Core, 1 Hyperthread
        limits: # set for each container in the pod
          memory: "2Gi"
          cpu: 2
```

A container cannot use the CPU more than the limit. A container can use more memory than the limit but will be terminated if it goes beyond limits constantly.

# <a name="taints-tolerations"></a>
# Taints and Tolerations
## Taints
Restrict nodes to accept certain pods

`kubectl taint nodes <node-name> <key>=<value>:<taint-effect>`

### Taint Effect
Defines what happens to the pod that do not meet the taint
* NoSchedule: pod will not be scheduled in the node
* PreferNoSchedule: system will try to avoid place the pod on the node
* NoExecute: new pod will not be scheduled on the node and existing pods will be evicted if they do not tolerate the taint

`kubectl taint nodes node1 app=blue:NoSchedule`

## Tolerations
Set on pods to allow the pod to be accepted to a node

```yaml
# pod spec
spec:
  containers:
    - name: my-pod
  tolerations:
    - key: "app"
      operator: "Equal"
      value: "blue"
      effect: "NoSchedule"
```

**Note: the Master node does not schedule any pods**

# <a name="node-selectors"></a>
# Node Selectors

Labels for node

```yaml
nodeSelector:
  size: Large
```

`kubectl label nodes <node-name> <key>=<value>`

# <a name="node-affinity"></a>
# Node Affinity

Ensure that pods are hosted on a particular node

Adavance capabilities

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
          - key: size # label key
            operator: In # or NotIn, Exists
            values:
            - Large # label value
```

## Node Affinity Types
Available:
* requiredDuringSchedulingIgnoredDuringExecution: mandate the pod to be placed on a node that matches the given affinity rule. If it cannot find one the pod will not be scheduled. DuringExecution: once scheduled changes to affinity rules will not affect the pod.
* preferredDuringSchedulingIgnoredDuringExecution: in case the matching node is not found, the scheduler will place the pod on any available node

Planned:
* requiredDuringSchedulingRequiredDuringExecution: changes to affinity rules will affect pods that have been scheduled.

## Taints & Tolerations vs Node Affinity
* Use Taints & Tolerations to prevent other pods being placed on to certain nodes.
* Use Node Affinity to prevent certain pods on certain nodes.

# <a name="observability"></a>
# Observability
## Readiness and Liveness Probes
### Readiness Probes
To connec the application's readiness with the container's readiness use the

```yaml
containers:
  - name: simple-webapp
    readinessProbe:
      httpGet: # http
        path: /api/ready
        port: 8080
      initialDelaySeconds: 10
      periodSeconds: 5
      failureThreshold: 8
    readinessProbe:
      tcpSocket: # tcp
        port: 3306
    readinessProbe:
      exec: # custom command
        command:
          - cat
          - /app/is_ready
```

### Liveness Probes
Periodically test whether the application within the container is healthy.

```yaml
containers:
  - name: simple-webapp
    livenessProbe:
      httpGet: # http
        path: /api/ready
        port: 8080
      initialDelaySeconds: 10
      periodSeconds: 5 # frequency
      failureThreshold: 8
    livenessProbe:
      tcpSocket: # tcp
        port: 3306
    livenessProbe:
      exec: # custom command
        command:
          - cat
          - /app/is_ready
```

## Container Logging

`kubectl logs -f <pod-name> <container-name>`

## <a name="monitoring-debugging"></a>
## Monitoring and Debugging
### Metric Server
**minikube**: `minikube addons enable metrics-server`
**others**: `git clone https://github.com/kubernetes-incubator/metrics-server.git && kubectl create -f deployment/1.8+`

`kubectl top node` - performance metrics of nodes

`kubectl top pod` - performance metrics of pods

# <a name="pod-design"></a>
# Pod Design
## Labels, Selectors and Annotations

Attach labels as per your needs

Specify conditions to filter specific objects
### Lables
Group things together

```yaml
metadata:
  name: simple-webapp
  labels:
    app: App1
    function: front-end
```

### Selector
Filter things

`kubectl get pods --selector <key>=<value>`

```yaml
spec:
  selector: # labels of pod to match
      matchLabels:
        app: App1
        function: front-end
  template: # pod templates
    metadata:
      labels:
        app: App1
        function: front-end
```

```yaml
# service config
spec:
  selector:
    app: App1
```

### Annotations
Record other details for informatory purpose, may be used for some kind of integration purpose
```yaml
metadata:
  name: test
  annotations:
    buildversion: 1.34
```

## <a name="updates-rollbacks"></a>
## Rolling Updates & Rollbacks in Deployments
### Rollout and Versioning
When you first create a deployment it triggers a new rollout. A new rollout creates a new deployment revision. Future rollouts create new deployment revisions.

### Deployment Strategy
1. Rolling strategy (default)
* Take down old version and bring up new version one by one
2. Recreate strategy
* Cause application downtime

### Commands
* Create: `kubectl create -f deployment.yaml`
* Get: `kubectl get deployments`
* Update: `kubectl apply -f deployment.yaml`, `kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1`
* Status: `kubectl rollout status deployment/myapp-deployment`, `kubectl rollout history deployment/myapp-deployment`
* Rollback: `kubectl rollout undo deployment/myapp-deployment`

### Updating a Deployment

Some handy examples related to updating a Kubernetes Deployment:

#### Creating a deployment, checking the rollout status and history:

In the example below, we will first create a simple deployment and inspect the rollout status and the rollout history:

```shell
master $ kubectl create deployment nginx --image=nginx:1.16
deployment.apps/nginx created
 
master $ kubectl rollout status deployment nginx
Waiting for deployment "nginx" rollout to finish: 0 of 1 updated replicas are available...
deployment "nginx" successfully rolled out 
 
master $ kubectl rollout history deployment nginx
deployment.extensions/nginx
REVISION CHANGE-CAUSE
1     <none>
```

#### Using the --revision flag:
Here the revision 1 is the first version where the deployment was created.

You can check the status of each revision individually by using the --revision flag:

```shell
master $ kubectl rollout history deployment nginx --revision=1
deployment.extensions/nginx with revision #1
 
Pod Template:
 Labels:    app=nginx    pod-template-hash=6454457cdb
 Containers:  nginx:  Image:   nginx:1.16
  Port:    <none>
  Host Port: <none>
  Environment:    <none>
  Mounts:   <none>
 Volumes:   <none>
```

#### Using the --record flag:

You would have noticed that the "change-cause" field is empty in the rollout history output. We can use the --record flag to save the command used to create/update a deployment against the revision number.

```shell
master $ kubectl set image deployment nginx nginx=nginx:1.17 --record
deployment.extensions/nginx image updated

master $ kubectl rollout history deployment nginx
deployment.extensions/nginx
 
REVISION CHANGE-CAUSE
1     <none>
2     kubectl set image deployment nginx nginx=nginx:1.17 --record=true
```

You can now see that the change-cause is recorded for the revision 2 of this deployment.

Let's make some more changes. In the example below, we are editing the deployment and changing the image from nginx:1.17 to nginx:latest while making use of the --record flag.

```shell
master $ kubectl edit deployments. nginx --record
deployment.extensions/nginx edited
 
master $ kubectl rollout history deployment nginx
REVISION CHANGE-CAUSE
1     <none>
2     kubectl set image deployment nginx nginx=nginx:1.17 --record=true
3     kubectl edit deployments. nginx --record=true

master $ kubectl rollout history deployment nginx --revision=3
deployment.extensions/nginx with revision #3

Pod Template: Labels:    app=nginx
    pod-template-hash=df6487dc Annotations: kubernetes.io/change-cause: kubectl edit deployments. nginx --record=true
 
 Containers:
  nginx:
  Image:   nginx:latest
  Port:    <none>
  Host Port: <none>
  Environment:    <none>
  Mounts:   <none>
 Volumes:   <none>
```

#### Undo a change:

Lets now rollback to the previous revision:

```shell
master $ kubectl rollout undo deployment nginx
deployment.extensions/nginx rolled back
 
master $ kubectl rollout history deployment nginx
deployment.extensions/nginxREVISION CHANGE-CAUSE
1     <none>
3     kubectl edit deployments. nginx --record=true
4     kubectl set image deployment nginx nginx=nginx:1.17 --record=true

master $ kubectl rollout history deployment nginx --revision=4
deployment.extensions/nginx with revision #4Pod Template:
 Labels:    app=nginx    pod-template-hash=b99b98f9
 Annotations: kubernetes.io/change-cause: kubectl set image deployment nginx nginx=nginx:1.17 --record=true
 Containers:
  nginx:
  Image:   nginx:1.17
  Port:    <none>
  Host Port: <none>
  Environment:    <none>
  Mounts:   <none>
 Volumes:   <none>
 
master $ kubectl describe deployments. nginx | grep -i image:
  Image:    nginx:1.17
```

With this, we have rolled back to the previous version of the deployment with the image = nginx:1.17

## <a name="jobs"></a>
## Jobs
Pods are meant to be running forever. By defaulk k8s the restartPolicy for pods is "Always".

A job is used to run a set of pods to perform given task to completion.

### Job Definition
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: math-add-job
spec:
  completions: 3 # desired successful executions
  parallelism: 3 # execution in parallelism
  template:
    spec:
      containers:
        - name: match-add
          image: ubuntu
          command: ['expr', '3', '+', '2']
      restartPolicy: Never
```

### CronJobs
```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: my-cron-job
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      completions: 3 # desired successful executions
      parallelism: 3 # execution in parallelism
      template:
        spec:
          containers:
            - name: match-add
              image: ubuntu
          restartPolicy: Never
```

# <a name="services"></a>
# Services
Enable communication between components within and outside of the application. Enable loose coupling between microservices.

* NodePort: listens to a port on the node and forward the request to a port on the pod running the application
* ClusterIP: virtual IP for internal communication
* LoadBalancer: distributes loads

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  ports:
    - nodePort: 3008
      targetPort: 80
      port: 80
  selector:
    app: myapp
    type: front-end
```

`kubectl create -f service.yaml`

## NodePort
Maps a port on the node to a port on the pod.

3 ports involved:
* TargetPort: port on the node
* Port: port of the Service
* NodePort: port of the node

When multiple pods have the same labels, the Service select all the pods as endpoints to receive requests, it uses random algorithm to distribute loads across pods.

When pods are distributed across multiple nodes, k8s automatically creates a Service that spans across all the nodes and map the targetPort to the same node port on all the nodes in the cluster.

## ClusterIP
Enables communication inside the cluster, enables loose coupling between components.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: back-end-service
spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  selector:
    app: my-app
    type: back-end
```

`kubectl create -f service.yaml`

## <a name="networking"></a>
## Ingress Networking
Route traffice to different services based on url path and implement ssl security. Layer 7 load balancer that is built-in to k8s cluster.

### Ingress Controller
K8s does not come with a ingress controller by default. There are different options of ingress controllers such as Nginx Ingress Controller.
* A NodePort service is required to expose it.
* A ConfigMap is required to feed Nginx service config data.
* A ServiceAccount is required to config the right roles and permissions.

### Ingress Resources
A set of rules and permissions that are set on the ingress controller created using definition files.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-a
spec:
  backend:
    serviceName: my-service
    servicePort: 80
```

Example of rules based on path:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-b
spec:
  rules:
    - http:
        paths:
          - path: /clothing
            backend:
              serviceName: clothing-service
              servicePort: 80
          - path: /watch
            backend:
              serviceName: watch-service
              servicePort: 80
```

Example of rules based on domain name:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-b
spec:
  rules:
    - host: clothing.my-store.com
      http:
        paths:
          - path: /clothing
            backend:
              serviceName: clothing-service
              servicePort: 80
    - host: watch.my-store.com
      http:
        paths:
        - path: /watch
          backend:
            serviceName: watch-service
            servicePort: 80
```

`kubectl get ingress`

* Default backend
