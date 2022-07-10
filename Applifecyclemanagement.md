
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
