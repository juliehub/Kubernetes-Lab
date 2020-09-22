1. Let us explore the environment first. How many nodes do you see in the cluster? 4
Including the master and workers
```bash
master $ kubectl get nodes
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   96s   v1.18.0
node01   Ready    <none>   67s   v1.18.0
node02   Ready    <none>   67s   v1.18.0node03   Ready    <none>   67s   v1.18.0
```
2. How many applications do you see hosted on the cluster? 2
Check the number of deployments
```bash
master $ kubectl get deployments
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
blue   3/3     3            3           65s
red    2/2     2            2           65s
```
3. On which nodes are the applications hosted on? node01 and node02
```bash
master $ kubectl get pod -o wide
NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE     NOMINATED NODE   READINESS GATES
blue-8455cd8cd7-755sh   1/1     Running   0          3m55s   10.244.2.3   node01   <none>           <none>
blue-8455cd8cd7-9s9tw   1/1     Running   0          3m55s   10.244.2.4   node01   <none>           <none>
blue-8455cd8cd7-dhg2p   1/1     Running   0          3m55s   10.244.3.3   node02   <none>           <none>
red-59d898f784-dcn7x    1/1     Running   0          3m55s   10.244.3.2   node02   <none>           <none>
red-59d898f784-dgfh9    1/1     Running   0          3m55s   10.244.2.2   node01   <none>           <none>
```
4. We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.
- Node node01 Unschedulable
- Pods evicted from node01
```bash
master $ kubectl drain node01 --ignore-daemonsets
node/node01 cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-flannel-ds-amd64-pfrdg, kube-system/kube-keepalived-vip-k9vj4, kube-system/kube-proxy-f7hzc
evicting pod default/blue-8455cd8cd7-755sh
evicting pod default/blue-8455cd8cd7-9s9tw
evicting pod default/red-59d898f784-dgfh9
pod/blue-8455cd8cd7-9s9tw evicted
pod/blue-8455cd8cd7-755sh evicted
pod/red-59d898f784-dgfh9 evicted
node/node01 evicted
```
5. What nodes are the apps on now?
```bash
master $ kubectl get pods -o wide
NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE     NOMINATED NODE   READINESS GATES
blue-8455cd8cd7-7zptn   1/1     Running   0          89s     10.244.1.4   node03   <none>           <none>
blue-8455cd8cd7-dhg2p   1/1     Running   0          8m38s   10.244.3.3   node02   <none>           <none>
blue-8455cd8cd7-gqfng   1/1     Running   0          90s     10.244.3.4   node02   <none>           <none>
red-59d898f784-dcn7x    1/1     Running   0          8m38s   10.244.3.2   node02   <none>           <none>
red-59d898f784-hkl6f    1/1     Running   0          89s     10.244.3.5   node02   <none>           <none>
```
6. The maintenance tasks have been completed. Configure the node to be schedulable again.
- Node01 is Schedulable
```bash
master $ kubectl uncordon node01
node/node01 uncordoned
```
7. How many pods are scheduled on node01 now? 0

8. Why are there no pods on node01?
Only when new pods are created they will be scheduled.

9. Why are there no pods placed on the master node?
Check the master node details.
Because master node has taints set on it.

Taints:             node-role.kubernetes.io/master:NoSchedule

```bash
master $  kubectl describe node master
Name:               master
Roles:              master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=master
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/master=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VtepMAC":"e2:b2:20:34:72:23"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 172.17.0.22
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Tue, 22 Sep 2020 07:01:23 +0000
Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  master
  AcquireTime:     <unset>
  RenewTime:       Tue, 22 Sep 2020 07:18:54 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason   Message
  ----                 ------  -----------------                 ------------------                ------   -------
  NetworkUnavailable   False   Tue, 22 Sep 2020 07:01:54 +0000   Tue, 22 Sep 2020 07:01:54 +0000   FlannelIsUp   Flannel is running on this node
  MemoryPressure       False   Tue, 22 Sep 2020 07:17:06 +0000   Tue, 22 Sep 2020 07:01:19 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Tue, 22 Sep 2020 07:17:06 +0000   Tue, 22 Sep 2020 07:01:19 +0000   KubeletHasNoDiskPressure   kubelet has no disk pressure
  PIDPressure          False   Tue, 22 Sep 2020 07:17:06 +0000   Tue, 22 Sep 2020 07:01:19 +0000   KubeletHasSufficientPID   kubelet has sufficient PID available
  Ready                True    Tue, 22 Sep 2020 07:17:06 +0000   Tue, 22 Sep 2020 07:02:03 +0000   KubeletReady   kubelet is posting ready status. AppArmor enabled
Addresses:
  InternalIP:  172.17.0.22
  Hostname:    master
Capacity:
  cpu:                2
  ephemeral-storage:  199545168Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             2040700Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  183900826525
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             1938300Ki
  pods:               110
System Info:
  Machine ID:                 fe5c44d6b8f24f3f7346114c5edeb653
  System UUID:                fe5c44d6b8f24f3f7346114c5edeb653
  Boot ID:                    a2a54f0a-5dfb-4894-9a89-457a6b6d6983
  Kernel Version:             4.15.0-101-generic
  OS Image:                   Ubuntu 18.04.4 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://19.3.6
  Kubelet Version:            v1.18.0
  Kube-Proxy Version:         v1.18.0
PodCIDR:                      10.244.0.0/24
PodCIDRs:                     10.244.0.0/24
Non-terminated Pods:          (7 in total)
  Namespace                   Name                              CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                   ----                              ------------  ----------  ---------------  -------------  ---
  kube-system                 coredns-66bff467f8-w2knw          100m (5%)     0 (0%)      70Mi (3%)        170Mi (8%)     17m
  kube-system                 etcd-master                       0 (0%)        0 (0%)      0 (0%)           0 (0%)         17m
  kube-system                 kube-apiserver-master             250m (12%)    0 (0%)      0 (0%)           0 (0%)         17m
  kube-system                 kube-controller-manager-master    200m (10%)    0 (0%)      0 (0%)           0 (0%)         17m
  kube-system                 kube-flannel-ds-amd64-tcvdb       100m (5%)     100m (5%)   50Mi (2%)        50Mi (2%)      17m
  kube-system                 kube-proxy-wtlld                  0 (0%)        0 (0%)      0 (0%)           0 (0%)         17m
  kube-system                 kube-scheduler-master             100m (5%)     0 (0%)      0 (0%)           0 (0%)         17m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                750m (37%)  100m (5%)
  memory             120Mi (6%)  220Mi (11%)
  ephemeral-storage  0 (0%)      0 (0%)
  hugepages-1Gi      0 (0%)      0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:
  Type     Reason                   Age                From                Message
  ----     ------                   ----               ----                -------
  Normal   Starting                 17m                kubelet, master     Starting kubelet.
  Warning  ImageGCFailed            17m                kubelet, master     failed to get imageFs info: unable to find data in memory cache
  Normal   NodeHasSufficientMemory  17m (x4 over 17m)  kubelet, master     Node master status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    17m (x4 over 17m)  kubelet, master     Node master status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     17m (x3 over 17m)  kubelet, master     Node master status is now: NodeHasSufficientPID
  Normal   NodeAllocatableEnforced  17m                kubelet, master     Updated Node Allocatable limit across pods
  Normal   Starting                 17m                kubelet, master     Starting kubelet.
  Normal   NodeHasSufficientMemory  17m                kubelet, master     Node master status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    17m                kubelet, master     Node master status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     17m                kubelet, master     Node master status is now: NodeHasSufficientPID
  Normal   NodeAllocatableEnforced  17m                kubelet, master     Updated Node Allocatable limit across pods
  Normal   Starting                 17m                kube-proxy, master  Starting kube-proxy.
  Normal   NodeReady                16m                kubelet, master     Node master status is now: NodeReady
```
10. It is now time to take down node02 for maintenance. Before you remove all workload from node02 answer the following question.
Can you drain node02 using the same command as node01? Try it.
Answer: No, you must force it
```bash
master $ kubectl drain node02 --ignore-daemonsets
node/node02 cordoned
error: unable to drain node "node02", aborting command...

There are pending nodes to be drained:
 node02
error: cannot delete Pods not managed by ReplicationController, ReplicaSet, Job, DaemonSet or StatefulSet (use --force to override): default/hr-app
```
11. Why do you need to force the drain?
Answer: node02 has a pod not part of a replicaset
```bash
master $ kubectl describe node node02
Name:               node02
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=node02
                    kubernetes.io/os=linux
Annotations:        flannel.alpha.coreos.com/backend-data: {"VtepMAC":"aa:53:99:fd:43:74"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 172.17.0.45
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Tue, 22 Sep 2020 07:01:52 +0000
Taints:             node.kubernetes.io/unschedulable:NoSchedule
Unschedulable:      true
Lease:
  HolderIdentity:  node02
  AcquireTime:     <unset>
  RenewTime:       Tue, 22 Sep 2020 07:36:45 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason   Message
  ----                 ------  -----------------                 ------------------                ------   -------
  NetworkUnavailable   False   Tue, 22 Sep 2020 07:02:08 +0000   Tue, 22 Sep 2020 07:02:08 +0000   FlannelIsUp   Flannel is running on this node
  MemoryPressure       False   Tue, 22 Sep 2020 07:32:30 +0000   Tue, 22 Sep 2020 07:01:52 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Tue, 22 Sep 2020 07:32:30 +0000   Tue, 22 Sep 2020 07:01:52 +0000   KubeletHasNoDiskPressure   kubelet has no disk pressure
  PIDPressure          False   Tue, 22 Sep 2020 07:32:30 +0000   Tue, 22 Sep 2020 07:01:52 +0000   KubeletHasSufficientPID   kubelet has sufficient PID available
  Ready                True    Tue, 22 Sep 2020 07:32:30 +0000   Tue, 22 Sep 2020 07:02:13 +0000   KubeletReady   kubelet is posting ready status. AppArmor enabled
Addresses:
  InternalIP:  172.17.0.45
  Hostname:    node02
Capacity:
  cpu:                2
  ephemeral-storage:  199545168Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             3989980Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  183900826525
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             3887580Ki
  pods:               110
System Info:
  Machine ID:                 238a6bfddb6b11d352a184665f69a122
  System UUID:                238a6bfddb6b11d352a184665f69a122
  Boot ID:                    7d23dc39-a5f8-45e8-a5c5-a30901ae2d5b
  Kernel Version:             4.15.0-101-generic
  OS Image:                   Ubuntu 18.04.4 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://19.3.6
  Kubelet Version:            v1.18.0
  Kube-Proxy Version:         v1.18.0
PodCIDR:                      10.244.3.0/24
PodCIDRs:                     10.244.3.0/24
Non-terminated Pods:          (8 in total)
  Namespace                   Name                           CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                   ----                           ------------  ----------  ---------------  -------------  ---
  default                     blue-8455cd8cd7-dhg2p          0 (0%)        0 (0%)      0 (0%)           0 (0%)         33m
  default                     blue-8455cd8cd7-gqfng          0 (0%)        0 (0%)      0 (0%)           0 (0%)         26m
  default                     hr-app                         0 (0%)        0 (0%)      0 (0%)           0 (0%)         4m24s
  default                     red-59d898f784-dcn7x           0 (0%)        0 (0%)      0 (0%)           0 (0%)         33m
  default                     red-59d898f784-hkl6f           0 (0%)        0 (0%)      0 (0%)           0 (0%)         26m
  kube-system                 kube-flannel-ds-amd64-xmcr5    100m (5%)     100m (5%)   50Mi (1%)        50Mi (1%)      34m
  kube-system                 kube-keepalived-vip-t2r7z      0 (0%)        0 (0%)      0 (0%)           0 (0%)         34m
  kube-system                 kube-proxy-8kh2j               0 (0%)        0 (0%)      0 (0%)           0 (0%)         34m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests   Limits
  --------           --------   ------
  cpu                100m (5%)  100m (5%)
  memory             50Mi (1%)  50Mi (1%)
  ephemeral-storage  0 (0%)     0 (0%)
  hugepages-1Gi      0 (0%)     0 (0%)
  hugepages-2Mi      0 (0%)     0 (0%)
Events:
  Type    Reason                   Age    From                Message
  ----    ------                   ----   ----                -------
  Normal  Starting                 34m    kubelet, node02     Starting kubelet.
  Normal  NodeHasSufficientMemory  34m    kubelet, node02     Node node02 status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    34m    kubelet, node02     Node node02 status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     34m    kubelet, node02     Node node02 status is now: NodeHasSufficientPID
  Normal  NodeAllocatableEnforced  34m    kubelet, node02     Updated Node Allocatable limit across pods
  Normal  Starting                 34m    kube-proxy, node02  Starting kube-proxy.
  Normal  NodeReady                34m    kubelet, node02     Node node02 status is now: NodeReady
  Normal  NodeNotSchedulable       3m38s  kubelet, node02     Node node02 status is now: NodeNotSchedulable
```
12. What is the name of the POD hosted on node02 that is not part of a replicaset? hr-app
```bash
master $ kubectl get pods -o wide
NAME                    READY   STATUS    RESTARTS   AGE    IP           NODE     NOMINATED NODE   READINESS GATES
blue-8455cd8cd7-7zptn   1/1     Running   0          28m    10.244.1.4   node03   <none>           <none>
blue-8455cd8cd7-dhg2p   1/1     Running   0          35m    10.244.3.3   node02   <none>           <none>
blue-8455cd8cd7-gqfng   1/1     Running   0          28m    10.244.3.4   node02   <none>           <none>
hr-app                  1/1     Running   0          7m6s   10.244.3.6   node02   <none>           <none>
red-59d898f784-dcn7x    1/1     Running   0          35m    10.244.3.2   node02   <none>           <none>
red-59d898f784-hkl6f    1/1     Running   0          28m    10.244.3.5   node02   <none>           <none>
```
13. What would happen to hr-app if node02 is drained forcefully?
Try it if you wish. 
Answer: hr-app will be lost forever

14. Drain node02 and mark it unschedulable
```bash
master $ kubectl drain node02 --ignore-daemonsets --force
node/node02 already cordoned
WARNING: deleting Pods not managed by ReplicationController, ReplicaSet, Job, DaemonSet or StatefulSet: default/hr-app; ignoring DaemonSet-managed Pods: kube-system/kube-flannel-ds-amd64-xmcr5, kube-system/kube-keepalived-vip-t2r7z, kube-system/kube-proxy-8kh2j
evicting pod default/blue-8455cd8cd7-gqfng
evicting pod default/blue-8455cd8cd7-dhg2p
evicting pod default/hr-app
evicting pod default/red-59d898f784-dcn7x
evicting pod default/red-59d898f784-hkl6f
pod/red-59d898f784-hkl6f evicted
pod/blue-8455cd8cd7-gqfng evicted
pod/hr-app evicted
pod/red-59d898f784-dcn7x evicted
pod/blue-8455cd8cd7-dhg2p evicted
node/node02 evicted
```
15. Node03 has our critical applications. We do not want to schedule any more apps on node03. Mark node03 as unschedulable but do not remove any apps currently running on it .
- Node03 Unschedulable
- Node03 has apps
```bash
master $ kubectl cordon node03
node/node03 cordoned
```




