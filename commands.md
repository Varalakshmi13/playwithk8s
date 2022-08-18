commads:

kubectl get po
kubrctl get po -n ns

###### print all pods and images 
'''
kubectl get pods --all-namespaces -o jsonpath="{.items[*].spec.containers[*].image}"
'''
###### Add taint
'''
kubectl taint nodes node1 key1=value1:NoSchedule
'''
###### Remove taint
'''
kubectl taint nodes node1 key1=value1:NoSchedule-
'''

###### lable a node as app=test
```
k label node node-name app=test
```

###### 
