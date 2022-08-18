commads:

kubectl get po
kubrctl get po -n ns

###### print all pods and images 
'''
kubectl get pods --all-namespaces -o jsonpath="{.items[*].spec.containers[*].image}"
'''
