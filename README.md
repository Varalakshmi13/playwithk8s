# playwithk8s


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





