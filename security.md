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
