### Monitor Cluster components

###### Node that consumes most cpu
```
kubectl top node

kubectl top node --sort-by='cpu' --no-headers | head -1 
```

###### Node that consumes most memory
```
kubectl top node --sort-by='memory' --no-headers | head -1
```

###### Pod that consumes more memory
```
k top pod --sort-by=memory
```

###### Pod that consumes least cpu
```
kubectl top pod --sort-by='cpu' --no-headers | tail -1 
```

### Managing application logs
###### Find the errors related to  app
```
k logs podname
```

###### find errors in multi container pod
```
 k logs webapp-2 -c simple-webapp
```
