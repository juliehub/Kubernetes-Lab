1. How many ReplicaSets exist on the system in the current(default) namespace?

Answer: 1
```bash
master $ kubectl get replicaset
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       11s
```
2. How many PODs are DESIRED in the new-replica-set?

Answer: 4

3. What is the image used to create the pods in the new-replica-set?
Answer: busybox777
```bash
master $ kubectl describe replicaset|grep -i image
    Image:      busybox777
```

```bash
master $ kubectl describe replicaset
Name:         new-replica-set
Namespace:    default
Selector:     name=busybox-pod
Labels:       <none>
Annotations:  <none>
Replicas:     4 current / 4 desired
Pods Status:  0 Running / 4 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  name=busybox-pod
  Containers:
   busybox-container:
    Image:      busybox777
    Port:       <none>
    Host Port:  <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  3m4s  replicaset-controller  Created pod: new-replica-set-tp6ck
  Normal  SuccessfulCreate  3m4s  replicaset-controller  Created pod: new-replica-set-d8dcq
  Normal  SuccessfulCreate  3m4s  replicaset-controller  Created pod: new-replica-set-75pwb
  Normal  SuccessfulCreate  3m4s  replicaset-controller  Created pod: new-replica-set-9jxcj
 ```
 4. How many PODs are READY in the new-replica-set?
 
 Answer: 0
 
 5. Why do you think the PODs are not ready?
 
 Answer: repository does not exist
 
 Error `Failed to pull image "busybox777": rpc error: code = Unknowndesc = Error response
 from daemon: pull access denied for busybox777, repository does not exist or may require 'docker login':
 denied: requested access to the resource is denied`
 
 ```bash
 master $ kubectl describe pods
Name:         new-replica-set-75pwb
Namespace:    default
Priority:     0
Node:         node01/172.17.0.52
Start Time:   Wed, 19 Aug 2020 11:04:05 +0000
Labels:       name=busybox-pod
Annotations:  <none>
Status:       Pending
IP:           10.244.1.5
IPs:
  IP:           10.244.1.5
Controlled By:  ReplicaSet/new-replica-set
Containers:
  busybox-container:
    Container ID:
    Image:         busybox777
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-zk56s (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-zk56s:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-zk56s
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  6m30s                  default-scheduler  Successfully assigned default/new-replica-set-75pwb to node01
  Normal   Pulling    4m44s (x4 over 6m27s)  kubelet, node01    Pulling image "busybox777"
  Warning  Failed     4m43s (x4 over 6m23s)  kubelet, node01    Failed to pull image "busybox777": rpc error: code = Unknowndesc = Error response from daemon: pull access denied for busybox777, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
  Warning  Failed     4m43s (x4 over 6m23s)  kubelet, node01    Error: ErrImagePull
  Normal   BackOff    4m31s (x6 over 6m22s)  kubelet, node01    Back-off pulling image "busybox777"
  Warning  Failed     85s (x19 over 6m22s)   kubelet, node01    Error: ImagePullBackOff


Name:         new-replica-set-9jxcj
Namespace:    default
Priority:     0
Node:         node01/172.17.0.52
Start Time:   Wed, 19 Aug 2020 11:04:05 +0000
Labels:       name=busybox-pod
Annotations:  <none>
Status:       Pending
IP:           10.244.1.6
IPs:
  IP:           10.244.1.6
Controlled By:  ReplicaSet/new-replica-set
Containers:
  busybox-container:
    Container ID:
    Image:         busybox777
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-zk56s (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-zk56s:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-zk56s
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  6m30s                  default-scheduler  Successfully assigned default/new-replica-set-9jxcj to node01
  Normal   Pulling    4m47s (x4 over 6m26s)  kubelet, node01    Pulling image "busybox777"
  Warning  Failed     4m46s (x4 over 6m22s)  kubelet, node01    Failed to pull image "busybox777": rpc error: code = Unknowndesc = Error response from daemon: pull access denied for busybox777, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
  Warning  Failed     4m46s (x4 over 6m22s)  kubelet, node01    Error: ErrImagePull
  Normal   BackOff    4m33s (x6 over 6m21s)  kubelet, node01    Back-off pulling image "busybox777"
  Warning  Failed     82s (x20 over 6m21s)   kubelet, node01    Error: ImagePullBackOff


Name:         new-replica-set-d8dcq
Namespace:    default
Priority:     0
Node:         node01/172.17.0.52
Start Time:   Wed, 19 Aug 2020 11:04:05 +0000
Labels:       name=busybox-pod
Annotations:  <none>
Status:       Pending
IP:           10.244.1.4
IPs:
  IP:           10.244.1.4
Controlled By:  ReplicaSet/new-replica-set
Containers:
  busybox-container:
    Container ID:
    Image:         busybox777
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    State:          Waiting
      Reason:       ErrImagePull
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-zk56s (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-zk56s:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-zk56s
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  6m30s                  default-scheduler  Successfully assigned default/new-replica-set-d8dcq to node01
  Normal   Pulling    4m46s (x4 over 6m27s)  kubelet, node01    Pulling image "busybox777"
  Warning  Failed     4m45s (x4 over 6m24s)  kubelet, node01    Failed to pull image "busybox777": rpc error: code = Unknowndesc = Error response from daemon: pull access denied for busybox777, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
  Warning  Failed     4m45s (x4 over 6m24s)  kubelet, node01    Error: ErrImagePull
  Normal   BackOff    4m29s (x6 over 6m23s)  kubelet, node01    Back-off pulling image "busybox777"
  Warning  Failed     84s (x19 over 6m23s)   kubelet, node01    Error: ImagePullBackOff


Name:         new-replica-set-tp6ck
Namespace:    default
Priority:     0
Node:         node01/172.17.0.52
Start Time:   Wed, 19 Aug 2020 11:04:05 +0000
Labels:       name=busybox-pod
Annotations:  <none>
Status:       Pending
IP:           10.244.1.3
IPs:
  IP:           10.244.1.3
Controlled By:  ReplicaSet/new-replica-set
Containers:
  busybox-container:
    Container ID:
    Image:         busybox777
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-zk56s (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-zk56s:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-zk56s
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  6m30s                  default-scheduler  Successfully assigned default/new-replica-set-tp6ck to node01
  Normal   Pulling    4m53s (x4 over 6m27s)  kubelet, node01    Pulling image "busybox777"
  Warning  Failed     4m52s (x4 over 6m25s)  kubelet, node01    Failed to pull image "busybox777": rpc error: code = Unknowndesc = Error response from daemon: pull access denied for busybox777, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
  Warning  Failed     4m52s (x4 over 6m25s)  kubelet, node01    Error: ErrImagePull
  Normal   BackOff    4m37s (x6 over 6m24s)  kubelet, node01    Back-off pulling image "busybox777"
  Warning  Failed     85s (x20 over 6m24s)   kubelet, node01    Error: ImagePullBackOff ```
```

6. Delete any one of the 4 PODs
```bash
master $ kubectl get pods
NAME                    READY   STATUS             RESTARTS   AGE
new-replica-set-75pwb   0/1     ImagePullBackOff   0          9m55s
new-replica-set-9jxcj   0/1     ImagePullBackOff   0          9m55s
new-replica-set-d8dcq   0/1     ImagePullBackOff   0          9m55s
new-replica-set-tp6ck   0/1     ImagePullBackOff   0          9m55s
master $ kubectl delete pod new-replica-set-75pwb
pod "new-replica-set-75pwb" deleted
```
7. How many PODs exist now?

Answer: Still 4
```bash
master $ kubectl get pods
NAME                    READY   STATUS             RESTARTS   AGE
new-replica-set-4rb78   0/1     ErrImagePull       0          35s
new-replica-set-9jxcj   0/1     ImagePullBackOff   0          11m
new-replica-set-d8dcq   0/1     ImagePullBackOff   0          11m
new-replica-set-tp6ck   0/1     ImagePullBackOff   0          11m
```

8. Why are there still 4 PODs, even after you deleted one?

Answer: ReplicaSet ensures that the desired number of PODs always run.

9. Create a ReplicaSet using the 'replicaset-definition-1.yaml' file located at /root/
Name: replicaset-1
There is an issue with the file, so fix it.

```bash
master $ kubectl create -f /root/replicaset-definition-1.yaml
error: unable to recognize "/root/replicaset-definition-1.yaml": no matches for kind "ReplicaSet" in version "v1"
master $ kubectl explain replicaset|grep VERSION
VERSION:  apps/v1
```
Edit the version from `v1` to `apps/v1`
```bash
master $ kubectl create -f /root/replicaset-definition-1.yaml
replicaset.apps/replicaset-1 created
```

10. Fix the issue in the replicaset-definition-2.yaml file and create a ReplicaSet using it.
Name: replicaset-2
This file is located at /root/
```bash
master $ kubectl create -f /root/replicaset-definition-2.yaml
The ReplicaSet "replicaset-2" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"tier":"nginx"}: `selector` does not match template `labels`
```
Edit the tier as `frontend`
```bash
master $ vi /root/replicaset-definition-2.yaml
master $ kubectl create -f /root/replicaset-definition-2.yaml
replicaset.apps/replicaset-2 created
```
