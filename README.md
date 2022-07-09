# playwithk8s

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

