# Kubernetes-Lab

#### 1. PODS
1. To show the number of PODs on the system in the current(default) namespace
```bash
master$ kubectl get pods
NAME            READY   STATUS    RESTARTS   AGE
newpods-4rtwk   1/1     Running   0          18s
newpods-cxv64   1/1     Running   0          18s
newpods-zvrzc   1/1     Running   0          18s
```
2. Create a new pod with the NGINX image
```bash
master$ kubectl run nginx --image iginx
pod/nginx created
```
3. Verify number of PODs
```bash
master$ kubectl get pods
NAME            READY   STATUS    RESTARTS   AGE
newpods-4rtwk   1/1     Running   0          18s
newpods-cxv64   1/1     Running   0          18s
newpods-zvrzc   1/1     Running   0          18s
nginx           1/1     Running   0          92s
```
4. What is the image used to create the one of the "newpods"?
Answer: busybox
```bash
master $ kubectl describe pod newpods-4rtwk
Name:         newpods-4rtwk
Namespace:    default
Priority:     0
Node:         node01/172.17.0.47
Start Time:   Wed, 19 Aug 2020 07:06:08 +0000
Labels:       tier=busybox
Annotations:  <none>
Status:       Running
IP:           10.244.1.6
IPs:
  IP:           10.244.1.6
Controlled By:  ReplicaSet/newpods
Containers:
  busybox:
    Container ID:  docker://a014869bc27a571dd54a781edd5c58fb2e9068a94aa67288f5b515cc882a4ac4
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:4f47c01fa91355af2865ac10fef5bf6ec9c7f42ad2321377c21e844427972977
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      1000
    State:          Running
      Started:      Wed, 19 Aug 2020 07:06:15 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-pqjjh (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-pqjjh:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-pqjjh
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  4m16s  default-scheduler  Successfully assigned default/newpods-4rtwk to node01
  Normal  Pulling    4m13s  kubelet, node01    Pulling image "busybox"
  Normal  Pulled     4m8s   kubelet, node01    Successfully pulled image "busybox"
  Normal  Created    4m8s   kubelet, node01    Created container busybox
  Normal  Started    4m8s   kubelet, node01    Started container busybox
```
5. Which nodes are these pods placed on? You must look at all the pods in detail to figure this out
Answer: node01/172.17.0.47

6. How many containers are part of the pod `webapp`? Note: We just created a new POD. Ignore the state of the POD for now.
```bash
master $ kubectl get pods
NAME            READY   STATUS             RESTARTS   AGE
newpods-4rtwk   1/1     Running            0          9m41s
newpods-cxv64   1/1     Running            0          9m41s
newpods-zvrzc   1/1     Running            0          9m41s
nginx           1/1     Running            0          10m
webapp          1/2     ImagePullBackOff   0          2m19s

master $ kubectl describe pod webapp
Name:         webapp
Namespace:    default
Priority:     0
Node:         node01/172.17.0.47
Start Time:   Wed, 19 Aug 2020 07:13:29 +0000
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:           10.244.1.7
IPs:
  IP:  10.244.1.7
Containers:
  nginx:
    Container ID:   docker://7cb56a096d2540ba44d19e6c0d0e6738029be53cdbc5fad7f77a242f0e52bb5b
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:b0ad43f7ee5edbc0effbc14645ae7055e21bc1973aee5150745632a24a752661
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 19 Aug 2020 07:13:32 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-pqjjh (ro)
  agentx:
    Container ID:
    Image:          agentx
    Image ID:
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-pqjjh (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-pqjjh:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-pqjjh
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                   From               Message
  ----     ------     ----                  ----               -------
  Normal   Scheduled  3m7s                  default-scheduler  Successfully assigned default/webapp to node01
  Normal   Pulling    3m6s                  kubelet, node01    Pulling image "nginx"
  Normal   Pulled     3m5s                  kubelet, node01    Successfully pulled image "nginx"
  Normal   Created    3m4s                  kubelet, node01    Created container nginx
  Normal   Started    3m4s                  kubelet, node01    Started container nginx
  Warning  Failed     2m23s (x3 over 3m3s)  kubelet, node01    Failed to pull image "agentx": rpc error: code = Unknown desc= Error response from daemon: pull access denied for agentx, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
  Normal   BackOff    103s (x5 over 3m2s)   kubelet, node01    Back-off pulling image "agentx"
  Warning  Failed     103s (x5 over 3m2s)   kubelet, node01    Error: ImagePullBackOff
  Normal   Pulling    91s (x4 over 3m4s)    kubelet, node01    Pulling image "agentx"
  Warning  Failed     90s (x4 over 3m3s)    kubelet, node01    Error: ErrImagePull
```
7.
