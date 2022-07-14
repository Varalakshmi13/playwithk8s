# Core Concepts

#### Pods
###### Create pod 
```
kubectl run nginx --image=nginx
```

###### To check the node of pod
```
kubectl get pods -o wide
kubectl describe po podname
```
###### Delete pod
```
kubectl delete pod podname
```
###### To get sample manifest file
```
kubectl run redis --image=redis123 --dry-run=client -o yaml > redis.yaml
kubectl create -f redis.yaml
kubectl get pods
```

###### to check what is the api version of a resource
```
kubectl explain replicaset | grep VERSION
```

#### Replicasets

###### in replica set match lables and template lables should match 
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: nginx
  template:
    metadata:
      labels:
        tier: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

###### Delete replica set 
```
kubectl delete replicaset <replicaset-name> 
kubectl delete -f <file-name>.yaml
```
###### Edis rs and change the image
```
kubectl edit replicaset new-replica-set
kubectl delete po <pod-name>
```

###### scale rs using cli
```
k scale rs new-replica-set --replicas=5
```
###### using yaml
```
kubectl edit rs name 
```

#### Deployments
###### Create deployment from yaml
`
kubectl create deployment --image=httpd:2.4-alpine httpd-frontend --dry-run=client -o yaml
`
copy the yaml into file and run
`
kubectl apply -f deploy.yaml
`

#### Namespaces
###### Count of namespaces
`kubectl get ns --no-headers | wc -l
`  no-headers counts by eliminating headers

###### Run pod in particular namespace
`
kubectl run redis --image=redis -n finance
`

###### pod is in which namespace
`
kubectl get pods --all-namespaces | grep blue
`

###### If pod and db service are in same namespace pod can access db service with Name

###### If the application and the db-service are in different namespaces in this case, we need to use the service name along with the namespace to access the database. The FQDN (fully Qualified Domain Name) for the db-service in this example would be db-service.dev.svc.cluster.local.. Note: You can also access it using the service name and namespace like this: db-service.dev

###### How many deployments existed on system
`
k get deploy --no-headers | wc -l
`

#### Services
###### Find the target port of a service / lables on a service / endpoints
`
k describe svc kubernetes
`

#### Imperative commands
###### creation of pod 
`
 kubectl run nginx-pod --image=nginx:alpine
`

###### Creation of pod with lables
`
 k run redis --image=redis:alpine --labels tier=db
  k run redis --image=redis:alpine -l tier=db
`

###### Create a service redis-service to expose the redis application within the cluster on port 6379.
`
k expose pod redis --port=6379 --name=redis-service
`
###### Create deployment
`
k create deployment webapp --image=kodekloud/webapp-color --replicas=3
`

###### Create a pod and expose it on container ports
`
k run custom-nginx --image=nginx --port=8080
`
###### Create new namespace
`
k create ns dev-ns
`

###### Create deployment
`
k create deploy redis-deploy -n dev-ns --image=redis --replicas=2
`
######  Create a pod and create a service and add target port
`
k run httpd --image httpd:alpine --port=80 --expose
`











Kubernetes components

pods
services
deployments
configmps
secrets
service accounts

liveness and Readiness probes

deployment strategies:
Rolling update
Canary deplaoyment
Bluegreen deployment


save list of pods to file:
kubectl get po > podslist.txt

list pods in all namespaces:
kubectl get po --all-namepaces

drain node:
kubectl drain node-name

better command is :
kubectl drain node-name --ignore-daemonsets

bring back the node using uncardon : 
kubectl uncordon node-name

