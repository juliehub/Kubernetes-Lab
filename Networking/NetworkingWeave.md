1. How many Nodes are part of this cluster?
Including master and worker nodes
Answer: 4
```bash
controlplane $ kubectl get nodes
NAME           STATUS   ROLES    AGE     VERSION
controlplane   Ready    master   4m6s    v1.19.0
node01         Ready    <none>   3m35s   v1.19.0
node02         Ready    <none>   3m36s   v1.19.0node03         Ready    <none>   3m36s   v1.19.0
```
2. What is the Networking Solution used by this cluster? `weeve`
```bash
controlplane $ cd /etc/cni/net.d
controlplane $ ls
10-weave.conflist
controlplane $ cat 10-weave.conflist
{
    "cniVersion": "0.3.0",
    "name": "weave",
    "plugins": [
        {
            "name": "weave",
            "type": "weave-net",
            "hairpinMode": true
        },
        {
            "type": "portmap",
            "capabilities": {"portMappings": true},
            "snat": true
        }
    ]
}
```
3. How many weave agents/peers are deployed in this cluster? 4
```bash
controlplane $ kubectl get pods -n kube-system
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-f9fd979d6-47dmd                1/1     Running   0          19m
coredns-f9fd979d6-xh9m9                1/1     Running   0          19m
etcd-controlplane                      1/1     Running   0          19m
kube-apiserver-controlplane            1/1     Running   0          19m
kube-controller-manager-controlplane   1/1     Running   0          19m
kube-proxy-2vbg8                       1/1     Running   0          19m
kube-proxy-4j4zv                       1/1     Running   0          19m
kube-proxy-5wwr5                       1/1     Running   0          19m
kube-proxy-fd79d                       1/1     Running   0          19m
kube-scheduler-controlplane            1/1     Running   0          19m
weave-net-9bwcc                        2/2     Running   0          19m
weave-net-bzrp5                        2/2     Running   0          19m
weave-net-jt7wm                        2/2     Running   0          19m
weave-net-kc7ww                        2/2     Running   0          19m
```
```bash
controlplane $ kubectl get pods -n kube-system -o wide | grep weave
weave-net-9bwcc                        2/2     Running   0          20m   172.17.0.48   controlplane   <none>           <none>
weave-net-bzrp5                        2/2     Running   0          20m   172.17.0.59   node03         <none>           <none>
weave-net-jt7wm                        2/2     Running   0          20m   172.17.0.57   node01         <none>           <none>
weave-net-kc7ww                        2/2     Running   0          20m   172.17.0.58   node02         <none>           <none>
```
4. On which nodes are the weave peers present?
Answer: one on every node

5. Identify the name of the bridge network/interface created by weave on each node
Answer: `weave`
```bash
controlplane $ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 02:42:ac:11:00:30 brd ff:ff:ff:ff:ff:ff
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:4b:cc:9b:23 brd ff:ff:ff:ff:ff:ff
4: datapath: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1376 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether 9e:e9:1e:cf:57:18 brd ff:ff:ff:ff:ff:ff
6: weave: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1376 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether a6:f4:07:2c:14:fd brd ff:ff:ff:ff:ff:ff
8: vethwe-datapath@vethwe-bridge: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1376 qdisc noqueue master datapath state UP mode DEFAULT group default
    link/ether 46:4d:ae:9b:2c:9c brd ff:ff:ff:ff:ff:ff
9: vethwe-bridge@vethwe-datapath: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1376 qdisc noqueue master weave state UP mode DEFAULT group default
    link/ether 9e:20:ea:0d:b4:d5 brd ff:ff:ff:ff:ff:ff
10: vxlan-6784: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 65535 qdisc noqueue master datapath state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether 52:f9:62:19:40:a2 brd ff:ff:ff:ff:ff:ff
```
6. What is the POD IP address range configured by weave?
Answer: 10.42.0.0/12 (10.x.x.x)
```bash
controlplane $ ip addr show weave
6: weave: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1376 qdisc noqueue state UP group default qlen 1000
    link/ether a6:f4:07:2c:14:fd brd ff:ff:ff:ff:ff:ff
    inet 10.42.0.0/12 brd 10.47.255.255 scope global weave
       valid_lft forever preferred_lft forever
    inet6 fe80::a4f4:7ff:fe2c:14fd/64 scope link
       valid_lft forever preferred_lft forever
```
```bash
controlplane $ ifconfig -a weave
weave: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1376
        inet 10.42.0.0  netmask 255.240.0.0  broadcast 10.47.255.255
        inet6 fe80::a4f4:7ff:fe2c:14fd  prefixlen 64  scopeid 0x20<link>
        ether a6:f4:07:2c:14:fd  txqueuelen 1000  (Ethernet)
        RX packets 53  bytes 3164 (3.1 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 6  bytes 488 (488.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
7. What is the default gateway configured on the PODs scheduled on node03?
Try scheduling a pod on node03 and check ip route output.
Answer:  10.46.0.0
```bash
controlplane $ kubectl run busybox --image=busybox --command sleep 1000 --dry-run=client -o yaml > pod.yaml
controlplane $ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - command:
    - sleep
    - "1000"
    image: busybox
    name: busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
controlplane $ cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  nodeName: node03
  containers:
  - command:
    - sleep
    - "1000"
    image: busybox
    name: busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
controlplane $ kubectl apply -f pod.yaml
pod/busybox created
controlplane $ kubectl exec -ti busybox -- sh
/ # ip route
default via 10.46.0.0 dev eth0
10.32.0.0/12 dev eth0 scope link  src 10.46.0.1
/ #
```

