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

###### How many nodes can host workloads in this cluster . (Check if there are any taints on nodes)
```
kubectl describe node controlplane
```

###### How many applications are hosted on the cluster?
`
kubectl get deployments
`

###### In order to ensure minimum downtime, upgrade the cluster one node at a time, while moving the workloads to another node. In the upcoming tasks you will get to practice how to do that.

###### What is the latest stable version available for upgrade?
```
kubeadm upgrade plan
```
###### Follow https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/ for upgrading node
```
On the controlplane node, run the command run the following commands:

apt update
This will update the package lists from the software repository.

apt install kubeadm=1.20.0-00
This will install the kubeadm version 1.20

kubeadm upgrade apply v1.20.0
This will upgrade kubernetes controlplane. Note that this can take a few minutes.

apt install kubelet=1.20.0-00 This will update the kubelet with the version 1.20.

You may need to restart kubelet after it has been upgraded.
Run: systemctl restart kubelet

```

###### Upgrade node01
```
On the node01 node, run the command run the following commands:

If you are on the master node, run ssh node01 to go to node01


apt update
This will update the package lists from the software repository.


apt install kubeadm=1.20.0-00
This will install the kubeadm version 1.20


kubeadm upgrade node
This will upgrade the node01 configuration.


apt install kubelet=1.20.0-00 This will update the kubelet with the version 1.20.


You may need to restart kubelet after it has been upgraded.
Run: systemctl restart kubelet


Type exit or enter CTL + d to go back to the controlplane node.
```

### Backup and Restore

###### What is the version of ETCD running on the cluster?

###### Look at the ETCD Logs using the command `kubectl logs etcd-controlplane -n kube-system` or check the image used by the ETCD pod: `kubectl describe pod etcd-controlplane -n kube-system`

###### At what address can you reach the ETCD cluster from the controlplane node?
`
kubectl describe pod etcd-controlplane -n kube-system 
`  and look for --listen-client-urls


###### . Take a snapshot of the ETCD database using the built-in snapshot functionality.(Refer https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/)
```
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
  snapshot save <backup-file-location>
```

###### Restore etcd
```
First Restore the snapshot:

root@controlplane:~# ETCDCTL_API=3 etcdctl  --data-dir /var/lib/etcd-from-backup \
snapshot restore /opt/snapshot-pre-boot.db


2022-03-25 09:19:27.175043 I | mvcc: restore compact to 2552
2022-03-25 09:19:27.266709 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32
root@controlplane:~# 
Note: In this case, we are restoring the snapshot to a different directory but in the same server where we took the backup (the controlplane node) As a result, the only required option for the restore command is the --data-dir.


Next, update the /etc/kubernetes/manifests/etcd.yaml:

We have now restored the etcd snapshot to a new path on the controlplane - /var/lib/etcd-from-backup, so, the only change to be made in the YAML file, is to change the hostPath for the volume called etcd-data from old directory (/var/lib/etcd) to the new directory (/var/lib/etcd-from-backup).

  volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data
With this change, /var/lib/etcd on the container points to /var/lib/etcd-from-backup on the controlplane (which is what we want)

When this file is updated, the ETCD pod is automatically re-created as this is a static pod placed under the /etc/kubernetes/manifests directory.

Note 1: As the ETCD pod has changed it will automatically restart, and also kube-controller-manager and kube-scheduler. Wait 1-2 to mins for this pods to restart. You can run a watch "docker ps | grep etcd" command to see when the ETCD pod is restarted.

Note 2: If the etcd pod is not getting Ready 1/1, then restart it by kubectl delete pod -n kube-system etcd-controlplane and wait 1 minute.

Note 3: This is the simplest way to make sure that ETCD uses the restored data after the ETCD pod is recreated. You don't have to change anything else.

If you do change --data-dir to /var/lib/etcd-from-backup in the YAML file, make sure that the volumeMounts for etcd-data is updated as well, with the mountPath pointing to /var/lib/etcd-from-backup (THIS COMPLETE STEP IS OPTIONAL AND NEED NOT BE DONE FOR COMPLETING THE RESTORE)


```
