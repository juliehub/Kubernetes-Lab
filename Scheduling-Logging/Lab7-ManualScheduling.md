1. Create new pod
```bash
controlplane $ cat nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginxcontrolplane
controlplane $ kubectl create -f nginx.yaml
pod/nginx created
```
2. Pod in the `pending` state because no scheduler presents
```bash
controlplane $ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   0/1     Pending   0          110s
controlplane $ kubectl get pods --namespace system
No resources found in system namespace.
```
3. Manually schedule the pod on `node01`
```bash
controlplane $ kubectl get nodes --show-labels
NAME           STATUS   ROLES    AGE   VERSION   LABELS
controlplane   Ready    master   35m   v1.18.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=controlplane,kubernetes.io/os=linux,node-role.kubernetes.io/master=
node01         Ready    <none>   34m   v1.18.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=node01,kubernetes.io/os=linux
controlplane $ cat nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx
  nodeName: node01
controlplane $ kubectl delete pod nginx
pod "nginx" deleted
controlplane $ kubectl create -f nginx.yaml
pod/nginx created
controlplane $ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          18s
```
