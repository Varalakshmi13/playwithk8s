
###### Maximum pods that can be unavailable can be seen in deployment strategy 
```
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
```

###### Recreate strategy
```
  strategy:
    type: Recreate
```

### commands-and-arguments
###### Sleep command
```
---
apiVersion: v1 
kind: Pod 
metadata:
  name: ubuntu-sleeper-2 
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
      - "sleep"
      - "5000"
```

######  ENTRYPOINT and CMD in Dockerfile run at cintainer startup.
###### The ENTRYPOINT in the Dockerfile will be overridden by the command in the pod definition.

###### Create a pod with the given specifications. By default it displays a blue background. Set the given command line arguments to change it to green
```
---
apiVersion: v1 
kind: Pod 
metadata:
  name: webapp-green
  labels:
      name: webapp-green 
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    args: ["--color", "green"]
```

###### or we can create pod using the command ```k run webapp-green --image=kodekloud/webapp-color --dry-run=client -o yaml > pod.yaml``` and edit as below
```
spec:
  containers:
  - image: kodekloud/webapp-color
    name: webapp-green
    args: ["--color", "green"]
```

### ENV variables

### ConfigMaps
######
```
k create configmap webapp-config-map --dry-run=client -o yaml >  configmap.yaml
```
###### and add data section in yaml and create cm

###### or we can give the direct command
```
kubectl create configmap webapp-config-map --from-literal=APP_COLOR=darkblue
```

###### Update the environment variable on the POD to use the newly created ConfigMap
```
spec:
  containers:
  - envFrom:
    - configMapRef:
         name: webapp-config-map
    image: kodekloud/webapp-color
    name: webapp-color
```

#### Secrets
###### Get secrets
```
k get sc
```
###### How many secrets or what is the type of secret 
```
k describe secret secretname 
```
###### Create secret using kubectl 
```
kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123
```

###### Refer the secret in a pod
```
---
apiVersion: v1 
kind: Pod 
metadata:
  labels:
    name: webapp-pod
  name: webapp-pod
  namespace: default 
spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    imagePullPolicy: Always
    name: webapp
    envFrom:
    - secretRef:
        name: db-secret
```


###### Add sidecar container and mount the volume to side car container Volume Mount: log-volume Mount Path: /var/log/event-simulator/
```
apiVersion: v1
kind: Pod
metadata:
  name: app
  namespace: elastic-stack
  labels:
    name: app
spec:
  containers:
  - name: app
    image: kodekloud/event-simulator
    volumeMounts:
    - mountPath: /log
      name: log-volume

  - name: sidecar
    image: kodekloud/filebeat-configured
    volumeMounts:
    - mountPath: /var/log/event-simulator/
      name: log-volume

  volumes:
  - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: DirectoryOrCreate
```

#### Init containers
###### Find which pod has init containers `k describe pod ` and find for Init Containers or `k describe po podname | grep Init`

###### Reason for status of pod can be obtained from Reason field in Init Container `k describe po podname `

###### How much time it will take for application to come up, add the command seconds of init containers

###### Update a pod to have init container
```
---
apiVersion: v1
kind: Pod
metadata:
  name: red
  namespace: default
spec:
  containers:
  - command:
    - sh
    - -c
    - echo The app is running! && sleep 3600
    image: busybox:1.28
    name: red-container
  initContainers:
  - image: busybox
    name: red-initcontainer
    command: 
      - "sleep"
      - "20"
```
###### Command to get the existing po yaml
```
kubectl get pod orange -o yaml > /root/orange.yaml
```

