
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
