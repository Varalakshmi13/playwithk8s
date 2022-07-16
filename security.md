### Certificate details

###### Identify the certificate file used for the kube-api server `kubectl describe pod kube-apiserver -n kube-syetem` and grep for tls-cert or `cat /etc/kubernetes/manifests/kube-apiserver.yaml` and find tls-cert

###### Identify the Certificate file used to authenticate kube-apiserver as a client to ETCD Server  `grep for etcd-certfile`

###### Identify the key used to authenticate kubeapi-server to the kubelet server `grep for -kubelet-client-key `

###### Identify the ETCD Server Certificate used to host ETCD server `grep for etcd-certfile`

###### What is the Common Name (CN) configured on the Kube API Server Certificate? 
```
openssl x509 -in file-path.crt -text -noout
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text.     and look for Subject CN.
```
###### Which of the below alternate names is not configured on the Kube API Server Certificate? --> same as above

###### What is the Common Name (CN) configured on the ETCD Server certificate?
```
openssl x509 -in /etc/kubernetes/pki/etcd/server.crt  -text -noout
```

### Certificates API
###### Create a CertificateSigningRequest object with the name akshay with the contents of the akshay.csr file

###### As of kubernetes 1.19, the API to use for CSR is certificates.k8s.io/v1.

###### Please note that an additional field called signerName should also be added when creating CSR. For client authentication to the API server we will use the built-in signer kubernetes.io/kube-apiserver-client.

```
Use this command to generate the base64 encoded format as following: -

cat akshay.csr | base64 -w 0
and finally, create a CSR name akshay as follows: -
```
```
---
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  groups:
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQXY4azZTTE9HVzcrV3JwUUhITnI2TGFROTJhVmQ1blNLajR6UEhsNUlJYVdlCmJ4RU9JYkNmRkhKKzlIOE1RaS9hbCswcEkwR2xpYnlmTXozL2lGSWF3eGVXNFA3bDJjK1g0L0lqOXZQVC9jU3UKMDAya2ZvV0xUUkpQbWtKaVVuQTRpSGxZNDdmYkpQZDhIRGFuWHM3bnFoenVvTnZLbWhwL2twZUVvaHd5MFRVMAo5bzdvcjJWb1hWZTVyUnNoMms4dzV2TlVPL3BBdEk4VkRydUhCYzRxaHM3MDI1ZTZTUXFDeHUyOHNhTDh1blJQCkR6V2ZsNVpLcTVpdlJNeFQrcUo0UGpBL2pHV2d6QVliL1hDQXRrRVJyNlMwak9XaEw1Q0ErVU1BQmd5a1c5emQKTmlXbnJZUEdqVWh1WjZBeWJ1VzMxMjRqdlFvbndRRUprNEdoayt2SU53SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBQi94dDZ2d2EweWZHZFpKZ1k2ZDRUZEFtN2ZiTHRqUE15OHByTi9WZEdxN25oVDNUUE5zCjEwRFFaVGN6T21hTjVTZmpTaVAvaDRZQzQ0QjhFMll5Szg4Z2lDaUVEWDNlaDFYZnB3bnlJMVBDVE1mYys3cWUKMkJZTGJWSitRY040MDU4YituK24wMy9oVkN4L1VRRFhvc2w4Z2hOaHhGck9zRUtuVExiWHRsK29jQ0RtN3I3UwpUYTFkbWtFWCtWUnFJYXFGSDd1dDJveHgxcHdCdnJEeGUvV2cybXNqdHJZUXJ3eDJmQnErQ2Z1dm1sVS9rME4rCml3MEFjbVJsMy9veTdqR3ptMXdqdTJvNG4zSDNKQ25SbE41SnIyQkZTcFVQU3dCL1lUZ1ZobHVMNmwwRERxS3MKNTdYcEYxcjZWdmJmbTRldkhDNnJCSnNiZmI2ZU1KejZPMUU9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

###### Get status of CSR
`
kubectl get csr
`
###### Status is in pending

###### Approve the csr
`
kubectl certificate approve akshay
`

######  What groups is this CSR requesting access to?
`
kubectl get csr agent-smith -o yaml > agent.yaml
`

###### Reject that request.
`kubectl certificate deny agent-smith`

###### Delete CSR
`kubectl delete csr agent-smith`


### Kubeconfig

###### Where is the default kubeconfig file located in the current environment?
```
ls -a
/root/.kube
```

###### How many clusters are defined in the default kubeconfig file?
```
kubectl config view
```

###### A new kubeconfig file named my-kube-config is created. It is placed in the /root directory. How many clusters are defined in that kubeconfig file?
```
kubectl config view --kubeconfig my-kube-config
```

###### to know the current context
```
kubectl config --kubeconfig=/root/my-kube-config current-context
```

###### to use the context
```
kubectl config --kubeconfig=/root/my-kube-config use-context research
```

###### We don't want to have to specify the kubeconfig file option on each command. Make the my-kube-config file the default kubeconfig.
`Replace the contents in the default kubeconfig file with the content from my-kube-config file.
`

###### With the current-context set to research, we are trying to access the cluster. However something seems to be wrong. Identify and fix the issue.Try running the kubectl get pods command and look for the error. All users certificates are stored at /etc/kubernetes/pki/users.

`
The path to certificate is incorrect in the kubeconfig file. Correct the certificate name which is available at /etc/kubernetes/pki/users/.
`


### RBAC

###### Inspect the environment and identify the authorization modes configured on the cluster.
`
Use the command kubectl describe pod kube-apiserver-controlplane -n kube-system and look for --authorization-mode.
`

###### How many roles exist in the default namespace?
`kubectl get roles`

###### Roles in all namespaces
`kubectl get roles --all-namespaces --no-headers | wc -l`

###### What are the resources the kube-proxy role in the kube-system namespace is given access to?
`kubectl describe role kube-proxy -n kube-system`

###### Which account is the kube-proxy role assigned to?
`
kubectl describe rolebinding kube-proxy -n kube-system
`

######  Inspect the permissions granted to the user. Check if the user can list pods in the default namespace.
`
kubectl get pods --as dev-user`

###### Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace.

```
kubectl create role developer --namespace=default --verb=list,create,delete --resource=pods
 kubectl create rolebinding dev-user-binding --namespace=default --role=developer --user=dev-user
```

###### manifest
```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "create","delete"]

---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```


###### Edit the role
```
kubectl edit role developer -n blue
```


###### What type of account does the Dashboard application use to query the Kubernetes API?
`check the url and find the error`

###### Inspect the Dashboard Application POD and identify the Service Account mounted on it.
`
kubectl get po -o yaml
`

###### At what location is the ServiceAccount credentials available within the pod?
`kubectl describe pod and check volume mount`

###### The default ServiceAccount has limited access. Create a new ServiceAccount named dashboard-sa.
`kubectl create serviceaccount dashboard-sa`

###### We just added additional permissions for the newly created dashboard-sa account using RBAC.
`checkout the files used to configure RBAC at /var/rbac`

######   


