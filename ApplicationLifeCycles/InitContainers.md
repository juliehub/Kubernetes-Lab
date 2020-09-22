1. Identify the pod that has an initContainer configured.
```bash
master $ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
blue    1/1     Running   0          12m
green   2/2     Running   0          12m
red     1/1     Running   0          12m
```
2. What is the image used by the initContainer on the blue pod?
busybox
```bash
master $ kubectl describe pod blue |grep -A5 Image
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:d366a4665ab44f0648d7a00ae3fae139d55e32f9712c67accd604bb55df9d05a
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
--
    Image:         busybox:1.28
    Image ID:      docker-pullable://busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
```
3. What is the state of the initContainer on pod blue? Terminated
```bash
master $ kubectl describe pod blu
Name:         blue
Namespace:    default
Priority:     0
Node:         node01/172.17.0.46
Start Time:   Tue, 22 Sep 2020 03:20:40 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.244.1.5
IPs:
  IP:  10.244.1.5
Init Containers:
  init-myservice:
    Container ID:  docker://017646f6d5d88403697926d874ae97e6ad17ceb86db88ce891af097bdbd19c87
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:d366a4665ab44f0648d7a00ae3fae139d55e32f9712c67accd604bb55df9d05a
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleep 5
    State:          Terminated
```
4. Why is the initContainer terminated? What is the reason?
Answer: The process completed successfully

5. We just created a new app named purple. How many initContainers does it have? 2
```bash
master $ kubectl describe pod purple
Name:         purple
Namespace:    default
Priority:     0
Node:         node01/172.17.0.46
Start Time:   Tue, 22 Sep 2020 03:40:11 +0000
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:           10.244.1.6
IPs:
  IP:  10.244.1.6
Init Containers:
  warm-up-1:
    Container ID:  docker://6b7e3853b34b864a8e58c2b96087c7f8f3103c338342919432866dfc00e880ed
    Image:         busybox:1.28
    Image ID:      docker-pullable://busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleep 600
    State:          Running
      Started:      Tue, 22 Sep 2020 03:40:13 +0000
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
  warm-up-2:
    Container ID:
    Image:         busybox:1.28
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleep 1200
    State:          Waiting
      Reason:       PodInitializing
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
Containers:
  purple-container:
    Container ID:
    Image:         busybox:1.28
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo The app is running! && sleep 3600
    State:          Waiting
      Reason:       PodInitializing
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
Conditions:
  Type              Status
  Initialized       False
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-npm7p:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-npm7p
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  8m34s  default-scheduler  Successfully assigned default/purple to node01
  Normal  Pulled     8m33s  kubelet, node01    Container image "busybox:1.28" already present on machine
  Normal  Created    8m33s  kubelet, node01    Created container warm-up-1
  Normal  Started    8m32s  kubelet, node01    Started container warm-up-1
```
6. How long after the creation of the POD will the application come up and be available to users? 30 mins.
Check the commands used in the initContainers. The first one sleeps for 600 seconds (10 minutes) and the second one sleeps for 1200 seconds (20 minutes)

7. Update the pod red to use an initContainer that uses the busybox image and sleeps for 20 seconds
Delete and re-create the pod if necessary. But make sure no other configurations change.
- Pod: red
- initContainer Configured Correctly
```bash
master $ kubectl get pod red -o yaml > red.yaml
master $ kubectl delete pod red
pod "red" deleted
master $ vi red.yaml
master $ cat red.yaml
apiVersion: v1
kind: Pod
metadata:
  name: red
  namespace: default
spec:
  containers:
  - command:
    - sh
    - -c
    - echo The app is running! && sleep 3600
    image: busybox:1.28
    imagePullPolicy: IfNotPresent
    name: red-container
  nodeName: node01
  initContainers:
  - image: busybox
    name: red-initcontainer
    command: ["sleep", "20"]
master $ kubectl create -f red.yaml
pod/red created
master $ kubectl describe pod red
Name:         red
Namespace:    default
Priority:     0
Node:         node01/172.17.0.46
Start Time:   Tue, 22 Sep 2020 04:01:54 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.244.1.7
IPs:
  IP:  10.244.1.7
Init Containers:
  red-initcontainer:
    Container ID:  docker://260f0e445a1f1688017f0dfcc9db467df891e2de6e03ad3e99855376094bde4c
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:d366a4665ab44f0648d7a00ae3fae139d55e32f9712c67accd604bb55df9d05a
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      20
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Tue, 22 Sep 2020 04:01:57 +0000
      Finished:     Tue, 22 Sep 2020 04:02:17 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
Containers:
  red-container:
    Container ID:  docker://6b3cdf3b9f9441616bb9b01cce2eb725a382d4cfd972d8f0914f1cce668ef983
    Image:         busybox:1.28
    Image ID:      docker-pullable://busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo The app is running! && sleep 3600
    State:          Running
      Started:      Tue, 22 Sep 2020 04:02:17 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-npm7p:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-npm7p
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason   Age   From             Message
  ----    ------   ----  ----             -------
  Normal  Pulling  28s   kubelet, node01  Pulling image "busybox"
  Normal  Pulled   27s   kubelet, node01  Successfully pulled image "busybox"
  Normal  Created  27s   kubelet, node01  Created container red-initcontainer
  Normal  Started  26s   kubelet, node01  Started container red-initcontainer
  Normal  Pulled   6s    kubelet, node01  Container image "busybox:1.28" already present on machine
  Normal  Created  6s    kubelet, node01  Created container red-container
  Normal  Started  6s    kubelet, node01  Started container red-container
```
8. A new application orange is deployed. There is something wrong with it. Identify and fix the issue.
Once fixed, wait for the application to run before checking solution.
```bash
master $ kubectl describe pod orange
Name:         orange
Namespace:    default
Priority:     0
Node:         node01/172.17.0.46
Start Time:   Tue, 22 Sep 2020 04:05:21 +0000
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:           10.244.1.8
IPs:
  IP:  10.244.1.8
Init Containers:
  init-myservice:
    Container ID:  docker://f3289d77bbdf07e50fdfc2029bcbc27ae2f47f0f0c880b0d2ffc97324439eaa0
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:d366a4665ab44f0648d7a00ae3fae139d55e32f9712c67accd604bb55df9d05a
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleeeep 2;
    State:          Terminated
      Reason:       Error
      Exit Code:    127
      Started:      Tue, 22 Sep 2020 04:05:43 +0000
      Finished:     Tue, 22 Sep 2020 04:05:43 +0000
    Last State:     Terminated
      Reason:       Error
      Exit Code:    127
      Started:      Tue, 22 Sep 2020 04:05:27 +0000
      Finished:     Tue, 22 Sep 2020 04:05:27 +0000
    Ready:          False
    Restart Count:  2
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
Containers:
  orange-container:
    Container ID:
    Image:         busybox:1.28
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo The app is running! && sleep 3600
    State:          Waiting
      Reason:       PodInitializing
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
Conditions:
  Type              Status
  Initialized       False
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-npm7p:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-npm7p
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  26s               default-scheduler  Successfully assigned default/orange to node01
  Normal   Pulling    6s (x3 over 25s)  kubelet, node01    Pulling image "busybox"
  Normal   Pulled     4s (x3 over 23s)  kubelet, node01    Successfully pulled image "busybox"
  Normal   Created    4s (x3 over 23s)  kubelet, node01    Created container init-myservice
  Normal   Started    4s (x3 over 23s)  kubelet, node01    Started container init-myservice
  Warning  BackOff    3s (x3 over 19s)  kubelet, node01    Back-off restarting failed container
```
8. Fix the type error in the initContainer definition
```bash
master $ kubectl describe pod orange
Name:         orange
Namespace:    default
Priority:     0
Node:         node01/172.17.0.46
Start Time:   Tue, 22 Sep 2020 04:05:21 +0000
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:           10.244.1.8
IPs:
  IP:  10.244.1.8
Init Containers:
  init-myservice:
    Container ID:  docker://f3289d77bbdf07e50fdfc2029bcbc27ae2f47f0f0c880b0d2ffc97324439eaa0
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:d366a4665ab44f0648d7a00ae3fae139d55e32f9712c67accd604bb55df9d05a
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleeeep 2;
    State:          Terminated
      Reason:       Error
      Exit Code:    127
      Started:      Tue, 22 Sep 2020 04:05:43 +0000
      Finished:     Tue, 22 Sep 2020 04:05:43 +0000
    Last State:     Terminated
      Reason:       Error
      Exit Code:    127
      Started:      Tue, 22 Sep 2020 04:05:27 +0000
      Finished:     Tue, 22 Sep 2020 04:05:27 +0000
    Ready:          False
    Restart Count:  2
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
Containers:
  orange-container:
    Container ID:
    Image:         busybox:1.28
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo The app is running! && sleep 3600
    State:          Waiting
      Reason:       PodInitializing
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-npm7p (ro)
Conditions:
  Type              Status
  Initialized       False
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-npm7p:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-npm7p
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  26s               default-scheduler  Successfully assigned default/orange to node01
  Normal   Pulling    6s (x3 over 25s)  kubelet, node01    Pulling image "busybox"
  Normal   Pulled     4s (x3 over 23s)  kubelet, node01    Successfully pulled image "busybox"
  Normal   Created    4s (x3 over 23s)  kubelet, node01    Created container init-myservice
  Normal   Started    4s (x3 over 23s)  kubelet, node01    Started container init-myservice
  Warning  BackOff    3s (x3 over 19s)  kubelet, node01    Back-off restarting failed container
master $ kubectl get pod -o yaml > orange.yaml
master $ kubectl delete pod orange
pod "orange" deleted
master $ vi orange.yaml
master $ kubectl apply -f orange.yaml
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
pod/blue configured
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
pod/green configured
pod/orange created
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
pod/purple configured
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
pod/red configured
```