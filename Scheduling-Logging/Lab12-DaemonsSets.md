1. How many DaemonSets are created in the cluster in all namespaces? 7
Check all namespaces
```bash
master $ kubectl get daemonsets --all-namespaces
NAMESPACE     NAME                      DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   kube-flannel-ds-amd64     2         2         2       2            2           <none>                   3m37s
kube-system   kube-flannel-ds-arm       0         0         0       0            0           <none>                   3m37s
kube-system   kube-flannel-ds-arm64     0         0         0       0            0           <none>                   3m37s
kube-system   kube-flannel-ds-ppc64le   0         0         0       0            0           <none>                   3m37s
kube-system   kube-flannel-ds-s390x     0         0         0       0            0           <none>                   3m37s
kube-system   kube-keepalived-vip       1         1         1       1            1           <none>                   3m36s
kube-system   kube-proxy                2         2         2       2            2           kubernetes.io/os=linux   3m39s
```
2. Which namespace are the DaemonSets created in? kube-system

3. Which of the below is a DaemonSet? daemonset.apps/kube-flannel-ds-amd64
```bash
master $ kubectl get all --all-namespaces
NAMESPACE     NAME                                          READY   STATUS    RESTARTS   AGEkube-system   pod/coredns-66bff467f8-9csjq                  1/1     Running   0          8m15s
kube-system   pod/coredns-66bff467f8-gmr85                  1/1     Running   0          8m15s
kube-system   pod/etcd-master                               1/1     Running   0          8m25s
kube-system   pod/katacoda-cloud-provider-58f89f7d9-tqndh   1/1     Running   4          8m14s
kube-system   pod/kube-apiserver-master                     1/1     Running   0          8m25s
kube-system   pod/kube-controller-manager-master            1/1     Running   0          8m25s
kube-system   pod/kube-flannel-ds-amd64-b9nb9               1/1     Running   0          8m15s
kube-system   pod/kube-flannel-ds-amd64-sdh7c               1/1     Running   0          8m2s
kube-system   pod/kube-keepalived-vip-pmn7w                 1/1     Running   0          7m10s
kube-system   pod/kube-proxy-pjfr6                          1/1     Running   0          8m2s
kube-system   pod/kube-proxy-tr5wn                          1/1     Running   0          8m15s
kube-system   pod/kube-scheduler-master                     1/1     Running   0          8m25s

NAMESPACE     NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                  8m37s
kube-system   service/kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   8m33s

NAMESPACE     NAME                                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR        AGE
kube-system   daemonset.apps/kube-flannel-ds-amd64     2         2         2       2            2           <none>        8m30s
kube-system   daemonset.apps/kube-flannel-ds-arm       0         0         0       0            0           <none>        8m30s
kube-system   daemonset.apps/kube-flannel-ds-arm64     0         0         0       0            0           <none>        8m30s
kube-system   daemonset.apps/kube-flannel-ds-ppc64le   0         0         0       0            0           <none>        8m30s
kube-system   daemonset.apps/kube-flannel-ds-s390x     0         0         0       0            0           <none>        8m30s
kube-system   daemonset.apps/kube-keepalived-vip       1         1         1       1            1           <none>        8m29s
kube-system   daemonset.apps/kube-proxy                2         2         2       2            2           kubernetes.io/os=linux   8m32s

NAMESPACE     NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/coredns                   2/2     2            2           8m33s
kube-system   deployment.apps/katacoda-cloud-provider   1/1     1            1           8m29s

NAMESPACE     NAME                                                DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/coredns-66bff467f8                  2         2         2       8m16s
kube-system   replicaset.apps/katacoda-cloud-provider-58f89f7d9   1         1         1       8m16s
```
4. On how many nodes are the pods scheduled by the DaemonSet `kube-proxy`
```bash
master $ kubectl describe daemonset kube-proxy --namespace=kube-system
Name:           kube-proxy
Selector:       k8s-app=kube-proxy
Node-Selector:  kubernetes.io/os=linux
Labels:         k8s-app=kube-proxy
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 2
Current Number of Nodes Scheduled: 2
Number of Nodes Scheduled with Up-to-date Pods: 2
Number of Nodes Scheduled with Available Pods: 2
Number of Nodes Misscheduled: 0
Pods Status:  2 Running / 0 Waiting / 0 Succeeded / 0 Failed
```
5. What is the image used by the POD deployed by the `kube-flannel-ds-amd64` DaemonSet? kubectl describe daemonset kube-flannel-ds-amd64 --namespace=kube-system
```bash
master $ kubectl describe daemonset kube-flannel-ds-amd64 --namespace=kube-system
Name:           kube-flannel-ds-amd64
Selector:       app=flannel
Node-Selector:  <none>
Labels:         app=flannel
                tier=node
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 2
Current Number of Nodes Scheduled: 2
Number of Nodes Scheduled with Up-to-date Pods: 2
Number of Nodes Scheduled with Available Pods: 2
Number of Nodes Misscheduled: 0
Pods Status:  2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           app=flannel
                    tier=node
  Service Account:  flannel
  Init Containers:
   install-cni:
    Image:      quay.io/coreos/flannel:v0.12.0-amd64
    Port:       <none>
    Host Port:  <none>
    Command:
      cp
    Args:
      -f
      /etc/kube-flannel/cni-conf.json
      /etc/cni/net.d/10-flannel.conflist
    Environment:  <none>
    Mounts:
      /etc/cni/net.d from cni (rw)
      /etc/kube-flannel/ from flannel-cfg (rw)
  Containers:
   kube-flannel:
    Image:      quay.io/coreos/flannel:v0.12.0-amd64
    Port:       <none>
    Host Port:  <none>
    Command:
      /opt/bin/flanneld
    Args:
      --ip-masq
      --kube-subnet-mgr
    Limits:
      cpu:     100m
      memory:  50Mi
    Requests:
      cpu:     100m
      memory:  50Mi
    Environment:
      POD_NAME:        (v1:metadata.name)
      POD_NAMESPACE:   (v1:metadata.namespace)
    Mounts:
      /etc/kube-flannel/ from flannel-cfg (rw)
      /run/flannel from run (rw)
  Volumes:
   run:
    Type:          HostPath (bare host directory volume)
    Path:          /run/flannel
    HostPathType:
   cni:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/cni/net.d
    HostPathType:
   flannel-cfg:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      kube-flannel-cfg
    Optional:  false
Events:
  Type    Reason            Age   From                  Message
  ----    ------            ----  ----                  -------
  Normal  SuccessfulCreate  20m   daemonset-controller  Created pod: kube-flannel-ds-amd64-b9nb9
  Normal  SuccessfulCreate  20m   daemonset-controller  Created pod: kube-flannel-ds-amd64-sdh7c
```
6. Deploy a DaemonSet for FluentD Logging.
Use the given specifications.
- Name: elasticsearch
- Namespace: kube-system
- Image: k8s.gcr.io/fluentd-elasticsearch:1.20
```bash
master $ kubectl create deployment elasticsearch --image=k8s.gcr.io/fluentd-elasticsearch:1.20 --dry-run -o yaml > elastic.yaml
```
Edit kind from `Deployment` to `DaemonSet`, Remove `replicas` and add `namespace: kube-system`
```bash
master $ cat elastic.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  creationTimestamp: null
  labels:
    app: elasticsearch
  name: elasticsearch
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: elasticsearch
    spec:
      containers:
      - image: k8s.gcr.io/fluentd-elasticsearch:1.20
        name: fluentd-elasticsearch
master $ kubectl apply -f elastic.yaml
daemonset.apps/elasticsearch created
master $ kubectl -n kube-system get ds elasticsearch
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
elasticsearch   1         1         1       1            1           <none>          102s
```
