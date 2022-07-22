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
