#### Manual Scheduling
###### Schedule pod after manuall assigning the node.
`
kubectl delete po podname.   
kubectl create -f nginx.yaml` 
or 
`kubectl replace --force -f nginx.yaml`

###### to watch the status till pod gets into running state
`
kubectl get po --watch
`
###### when we want to move the pod from one node to another, to pod will be deleted first and it will be recreated in the other node

###### count of pods matching with lable
`
k get po -l env=dev --no-headers | wc -l` or ` 
kubectl get pods --selector env=dev
`

###### Get all objects related to prod lable
`
 k get all -l env=prod --no-headers | wc -l
`

###### Find a pod having multiple lables
`
k get po -l env=prod,bu=finance,tier=frontend
`

#### Taints and Tolerations

###### Get taints on node
`
kubectl describe node node01 | grep -i taints
`

###### Create a taint on node
`
kubectl taint nodes node01 spray=mortein:NoSchedule
`

###### Create a pod manifest file
```
---
apiVersion: v1
kind: Pod
metadata:
  name: mosquito
spec:
  containers:
  - image: nginx
    name: mosquito
```

###### Create a pod with toleration
```
---
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - key: spray
    value: mortein
    effect: NoSchedule
    operator: Equal
```

###### Remove taint on control plane node
```
kubectl taint nodes node1 key1=value1:NoSchedule-

kubectl taint nodes controlplane node-role.kubernetes.io/master:NoSchedule-
```
#### Node Affinity
###### Label a node
`
kubectl label node node01 color=blue
`

###### Create a new deployment named red with the nginx image and 2 replicas, and ensure it gets placed on the controlplane node only.Use the label key - node-role.kubernetes.io/master - which is already set on the controlplane node.
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: red
spec:
  replicas: 2
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
```
