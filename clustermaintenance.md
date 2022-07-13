#### OS upgrades
###### How many applications do you see hosted on the cluster? Count the number of deployments
`
kubectl get deployments
`

###### Which nodes applications hosted on
`
k get po -o wide
`

###### We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.
`
kubectl drain node01 --ignore-daemonsets
`

####### Configure the node01 to be scheduled againkubectl cordon node01
`
kubectl uncordon node01
`
###### Only when new pods are created they will be scheduled on node01

--
###### If there is a  pod scheduled on node01 which is not part of a replicaset.The drain command will not work in this case. To forcefully drain the node we now have to use the --force flag.

###### if it is drained forcefully , tyhe single pod will be lost forever

######  Command to ensure that no new pods are scheduled on this node and the existing pods will not be affected by this operation.
`
kubectl cordon node01
`

### Cluster upgrade process

###### What is the current version of the cluster
`
kubectl get nodes
`
