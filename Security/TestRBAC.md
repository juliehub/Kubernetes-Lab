1. Inspect the environment and identify the authorization modes configured on the cluster.
Check the kube-api server settings
`--authorization-mode=Node,RBAC`
```bash
master $ kubectl describe pod kube-apiserver-master -n kube-system
Name:                 kube-apiserver-master
Namespace:            kube-system
Priority:             2000000000
Priority Class Name:  system-cluster-critical
Node:                 master/172.17.0.45
Start Time:           Sun, 27 Sep 2020 23:51:47 +0000
Labels:               component=kube-apiserver
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 172.17.0.45:6443
                      kubernetes.io/config.hash: 59ff0d845ed10fc604b9978363ddc35e
                      kubernetes.io/config.mirror: 59ff0d845ed10fc604b9978363ddc35e
                      kubernetes.io/config.seen: 2020-09-27T23:51:40.271854264Z
                      kubernetes.io/config.source: file
Status:               Running
IP:                   172.17.0.45
IPs:
  IP:           172.17.0.45
Controlled By:  Node/master
Containers:
  kube-apiserver:
    Container ID:  docker://60887528b680fa441221d678710330573f20c5d79ce2955443db1704cd9daf98
    Image:         k8s.gcr.io/kube-apiserver:v1.18.0
    Image ID:      docker-pullable://k8s.gcr.io/kube-apiserver@sha256:fc4efb55c2a7d4e7b9a858c67e24f00e739df4ef5082500c2b60ea0903f18248
    Port:          <none>
    Host Port:     <none>
    Command:
      kube-apiserver
      --advertise-address=172.17.0.45
      --allow-privileged=true
      --authorization-mode=Node,RBAC
      --client-ca-file=/etc/kubernetes/pki/ca.crt
      --enable-admission-plugins=NodeRestriction
      --enable-bootstrap-token-auth=true
      --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
      --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
      --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
      --etcd-servers=https://127.0.0.1:2379
      --insecure-port=0
      --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
      --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
      --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
      --requestheader-allowed-names=front-proxy-client
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
      --requestheader-extra-headers-prefix=X-Remote-Extra-
      --requestheader-group-headers=X-Remote-Group
      --requestheader-username-headers=X-Remote-User
      --secure-port=6443
      --service-account-key-file=/etc/kubernetes/pki/sa.pub
      --service-cluster-ip-range=10.96.0.0/12
      --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
      --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
    State:          Running
      Started:      Sun, 27 Sep 2020 23:51:31 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        250m
    Liveness:     http-get https://172.17.0.45:6443/healthz delay=15s timeout=15s period=10s #success=1 #failure=8
    Environment:  <none>
    Mounts:
      /etc/ca-certificates from etc-ca-certificates (ro)
      /etc/kubernetes/pki from k8s-certs (ro)
      /etc/ssl/certs from ca-certs (ro)
      /usr/local/share/ca-certificates from usr-local-share-ca-certificates (ro)
      /usr/share/ca-certificates from usr-share-ca-certificates (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  ca-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/ssl/certs
    HostPathType:  DirectoryOrCreate
  etc-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/ca-certificates
    HostPathType:  DirectoryOrCreate
  k8s-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/pki
    HostPathType:  DirectoryOrCreate
  usr-local-share-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /usr/local/share/ca-certificates
    HostPathType:  DirectoryOrCreate
  usr-share-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /usr/share/ca-certificates
    HostPathType:  DirectoryOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute
Events:            <none>
```
2. How many roles exist in the default namespace? 0
```bash
master $ kubectl get roles
No resources found in default namespace.
```
3. How many roles exist in all namespaces together? 12
```bash
master $ kubectl get roles --all-namespaces
NAMESPACE     NAME                                             CREATED AT
blue          developer                                        2020-09-27T23:55:15Z
kube-public   kubeadm:bootstrap-signer-clusterinfo             2020-09-27T23:51:39Z
kube-public   system:controller:bootstrap-signer               2020-09-27T23:51:38Z
kube-system   extension-apiserver-authentication-reader        2020-09-27T23:51:38Z
kube-system   kube-proxy                                       2020-09-27T23:51:40Z
kube-system   kubeadm:kubelet-config-1.18                      2020-09-27T23:51:38Z
kube-system   kubeadm:nodes-kubeadm-config                     2020-09-27T23:51:38Z
kube-system   system::leader-locking-kube-controller-manager   2020-09-27T23:51:38Z
kube-system   system::leader-locking-kube-scheduler            2020-09-27T23:51:38Z
kube-system   system:controller:bootstrap-signer               2020-09-27T23:51:38Z
kube-system   system:controller:cloud-provider                 2020-09-27T23:51:38Z
kube-system   system:controller:token-cleaner                  2020-09-27T23:51:38Z
master $ kubectl get roles --all-namespaces|wc -l
```
4. What are the resources the kube-proxy role in the kube-system namespace is given access to? `configmaps`
```bash
master $ kubectl describe role kube-proxy -n kube-system
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 [kube-proxy]    [get]
```
5. What actions can the kube-proxy role perform on configmaps? `get`

6. Which of the following statements are true?
kube-proxy role can get details of configmap object by the name kube-proxy

7. Which account is the kube-proxy role assigned to it? `system:bootstrappers:kubeadm:default-node-token`
```bash
master $ kubectl describe rolebinding kube-proxy -n kube-system
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  kube-proxy
Subjects:
  Kind   Name                                             Namespace
  ----   ----                                             ---------
  Group  system:bootstrappers:kubeadm:default-node-token
```
8. A user `dev-user` is created. User's details have been added to the kubeconfig file. Inspect the permissions granted to the user. Check if the user can list pods in the default namespace.
Use the `--as dev-user` option with kubectl to run commands as the dev-user
Answer: `dev-user` does not have permissions to list pods
```bash
master $ kubectl get pods --as dev-user
Error from server (Forbidden): pods is forbidden: User "dev-user" cannot list resource "pods" in API group "" in the namespace "default"
```
9. Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace.
Use the given spec:
- Role: developer
- Role Resources: pods
- Role Actions: list
- Role Actions: create
- RoleBinding: dev-user-binding
- RoleBinding: Bound to dev-user
```bash
master $ cat developer-role.yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "create"]
master $ kubectl create -f developer-role.yaml
role.rbac.authorization.k8s.io/developer created
```
```bash
master $ vi devuser-developer-rolebinding.yaml
master $ cat devuser-developer-rolebinding.yaml
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
master $ kubectl create -f devuser-developer-rolebinding.yaml
rolebinding.rbac.authorization.k8s.io/dev-user-binding created
```
10. The dev-user is trying to get details about the dark-blue-app pod in the blue namespace. Investigate and fix the issue.
We have created the required roles and rolebindings, but something seems to be wrong. 
```bash
master $ kubectl get roles --namespace blue
NAME        CREATED AT
developer   2020-09-28T05:32:24Z
master $ kubectl describe role developer --namespace blue
Name:         developer
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  pods       []                 [blue-app]      [get watch create delete]
```
```bash
master $ cat developer-role.yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: blue
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "delete", "create"]
  resourceNames: ["dark-blue-app"]

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
master $ kubectl delete role developer --namespace blue
role.rbac.authorization.k8s.io "developer" deleted
master $ kubectl delete rolebinding dev-user-binding
rolebinding.rbac.authorization.k8s.io "dev-user-binding" deleted
master $ kubectl create -f developer-role.yaml
role.rbac.authorization.k8s.io/developer created
rolebinding.rbac.authorization.k8s.io/dev-user-binding created
master $ kubectl get roles --namespace blue
NAME        CREATED AT
developer   2020-09-28T06:23:03Z
master $ kubectl describe role developer --namespace blue
Name:         developer
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources  Non-Resource URLs  Resource Names   Verbs
  ---------  -----------------  --------------   -----
  pods       []                 [dark-blue-app]  [get watch delete create]
```
11. Grant the dev-user permissions to create deployments in the blue namespace.
Remember to add both groups "apps" and "extensions"
```bash
master $ cat dev-user-deploy.yaml
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: blue
  name: deploy-role
rules:
- apiGroups: ["apps", "extensions"]
  resources: ["deployments"]
  verbs: ["create"]

---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-deploy-binding
  namespace: blue
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: deploy-role
  apiGroup: rbac.authorization.k8s.io
master $ kubectl create -f dev-user-deploy.yaml
role.rbac.authorization.k8s.io/deploy-role created
rolebinding.rbac.authorization.k8s.io/dev-user-deploy-binding created
master $ kubectl get roles --namespace blue
NAME          CREATED AT
deploy-role   2020-09-28T06:25:45Z
developer     2020-09-28T06:23:03Z
master $ kubectl describe role deploy-role --namespace blue
Name:         deploy-role
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources               Non-Resource URLs  Resource Names  Verbs
  ---------               -----------------  --------------  -----
  deployments.apps        []                 []              [create]
  deployments.extensions  []                 []              [create]
```