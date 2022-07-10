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
###### Affinity with key and value
```
affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```
###### if the pod failed with Reason:       OOMKilled , that means pod ran out of memory

###### create pod yaml
`
k get po elephant -o yaml> elephant.yaml
` modify and run `kubectl replace -f elephant.yaml --force` 

#### Daemon Sets
###### Get Daemon sets in all ns
`
k get ds --all-namespaces
`
###### Create a daemon set
```
kubectl create deployment elasticsearch --image=k8s.gcr.io/fluentd-elasticsearch:1.20 -n kube-system --dry-run=client -o yaml > fluentd.yaml
Modify the kind and remove replicas
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    app: elasticsearch
  name: elasticsearch
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - image: k8s.gcr.io/fluentd-elasticsearch:1.20
        name: fluentd-elasticsearch
```

#### Statis pods
###### Find static pods in all namespaces
```
kubectl get pods --all-namespaces 

and look for those with -controlplane appended in the name
```

###### kube-proxy is deployed as a DaemonSet and hence, it is not a staic pod.

###### By default, static pods are created for the controlplane components and hence, they are only created in the controlplane node.

##### Path of static pod

###### Run the command `ps -aux | grep kubelet ` and identify the `config file - --config=/var/lib/kubelet/config.yaml.` Then check in the config file for staticPodPath.

##### Create static pod
###### Go to `/etc/kubernetes/manifests` and get the static pod yaml from doc and add `command ` or we can use kubectl command `kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml`

##### Remove static pod scheduled on different node
###### First check the pod is scheduled on which node
`
k get po -o wide
`
###### Get ip of that node and do ssh
`
k get no -o wide
`

###### Run the command `ps -aux | grep kubelet | grep config ` and identify the `config file - --config=/var/lib/kubelet/config.yaml.` Then check in the config file for staticPodPath.

###### go to static pod path and delete the manifest file
`
rm -rf pod.yaml
`

### Multiple schedulers
###### create a configmap that the scheduler will employ using the concept of ConfigMap as a volume.Create a configmap with name my-scheduler-config using the content of file /root/my-scheduler-config.yaml
`
kubectl create -n kube-system configmap my-scheduler-config --from-file=/root/my-scheduler-config.yaml
`

###### Add scheduler to the pod schedulerName: my-scheduler
