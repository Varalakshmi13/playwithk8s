### Ports
```
ETCD  : 2379
Api Server  : 6443
Nodeport  : 30000 to 32767

```

###### kubeadm setup
```
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

###### Non Kubeadm setup
```
cat /etc/systemd/system/kubeapiserver.service
```


###### You can also see the running process and effective process usinf
```
ps -aux | grep kube-apiserver
```

######  key difference is that the replication controller only supports equality-based selectors whereas the replica set supports set-based selectors.

###### Mysql connection from one ns to other
```
mysql.connect("db-service.dev.svc.cluster.local")
mysql.connect("servicename.ns.svc.domainname")
```

###### kubectl config set-context $(kubectl config current-context) --namespace=prod

###### Difference b/w nodeport and load balancer
```
Node port has many ips and Load balancer has single ip
```

###### Refer imperative vs declarative section 2


###### How to schedule a pod if there is no scheduler
```
we can assign nodename to pod only when we are creating. If the pod is already present,  its not possible to assign the node
Another way is to create and "pod binding object" and send post request to pod binding API
in json format using curl command
```

### Memory
###### k8s default is 1 vcpu if you dint specify , Memory of 512Mi
###### Container "cannot" use more "cpu" than its limit
###### ontainer "can" use more "memory" than its limit


###### kubeproxy is deployed as daemonset

###### pod manifest oath can be found in 
```
kubelet.service, we have 
--pod-manifest-path = /etc/kubernetes/manifests
```
