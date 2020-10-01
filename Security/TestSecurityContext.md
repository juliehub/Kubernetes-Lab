1. What is the user used to execute the sleep process within the 'ubuntu-sleeper' pod? `root`
in the current(default) namespace
```bash
master $ kubectl exec ubuntu-sleeper -- whoami
root
```
2. Edit the pod 'ubuntu-sleeper' to run the sleep process with user ID 1010.
Note: Only make the necessary changes. Do not modify the name or image of the pod.
- Pod Name: ubuntu-sleeper
- Image Name: ubuntu
- SecurityContext: User 1010
```bash
master $ kubectl get pod ubuntu-sleeper -o yaml > ubuntu-sleeper.yaml
master $ vi ubuntu-sleeper.yaml
master $ cat ubuntu-sleeper.yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
  namespace: default
spec:
  securityContext:
    runAsUser: 1010
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    name: ubuntu-sleeper
```
```bash
master $ kubectl delete pod ubuntu-sleeper
pod "ubuntu-sleeper" deleted
master $ kubectl apply -f /var/answers/answer-2.yaml
pod/ubuntu-sleeper created
master $ kubectl exec ubuntu-sleeper -- whoami
whoami: cannot find name for user ID 1010
command terminated with exit code 1
```
3. A Pod definition file named 'multi-pod.yaml' is given. With what user are the processes in the 'web' container started? `1002`
The pod is created with multiple containers and security contexts defined at the POD and Container level
```bash
master $ cat multi-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  -  image: ubuntu
     name: web
     command: ["sleep", "5000"]
     securityContext:
      runAsUser: 1002

  -  image: ubuntu
     name: sidecar
     command: ["sleep", "5000"]
```
4. With what user are the processes in the 'sidecar' container started? `1001`
The pod is created with multiple containers and security contexts defined at the POD and Container level

5. Try to run the below command in the pod 'ubuntu-sleeper' to set the date. Are you allowed to set date on the POD? No
date -s '19 APR 2012 11:14:00'
```bash
master $ kubectl exec -it ubuntu-sleeper -- date -s '19 APR 2012 11:14:00'
date: cannot set date: Operation not permitted
Thu Apr 19 11:14:00 UTC 2012
command terminated with exit code 1
```
6. Update pod 'ubuntu-sleeper' to run as Root user and with the 'SYS_TIME' capability.
Note: Only make the necessary changes. Do not modify the name of the pod.
- Pod Name: ubuntu-sleeper
- Image Name: ubuntu
- SecurityContext: Capability SYS_TIME
```bash
master $ kubectl get pod ubuntu-sleeper -o yaml > u.yaml
master $ vi u.yaml
master $ cat u.yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
  namespace: default
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    name: ubuntu-sleeper
    securityContext:
      capabilities:
        add: ["SYS_TIME"]
master $ kubectl delete pod ubuntu-sleeper
pod "ubuntu-sleeper" deleted
master $ kubectl apply -f u.yaml
pod/ubuntu-sleeper created
master $ kubectl describe pod ubuntu-sleeper
Name:         ubuntu-sleeper
Namespace:    default
Priority:     0
Node:         node01/172.17.0.10
Start Time:   Thu, 01 Oct 2020 00:58:28 +0000
Labels:       <none>
Annotations:  Status:  Running
IP:           10.244.1.6
IPs:
  IP:  10.244.1.6
Containers:
  ubuntu-sleeper:
    Container ID:  docker://bb6a24dade3daf21b72bd68a0e70573da408f1d263f857dc0117dd3dcab9cef2
    Image:         ubuntu
    Image ID:      docker-pullable://ubuntu@sha256:bc2f7250f69267c9c6b66d7b6a81a54d3878bb85f1ebb5f951c896d13e6ba537
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      4800
    State:          Running
      Started:      Thu, 01 Oct 2020 00:58:31 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8m8jm (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-8m8jm:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-8m8jm
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason   Age   From             Message
  ----    ------   ----  ----             -------
  Normal  Pulling  63s   kubelet, node01  Pulling image "ubuntu"
  Normal  Pulled   61s   kubelet, node01  Successfully pulled image "ubuntu"
  Normal  Created  61s   kubelet, node01  Created container ubuntu-sleeper
  Normal  Started  61s   kubelet, node01  Started container ubuntu-sleeper
```
7. Now try to run the below command in the pod to set the date. If the security capability was added correctly, it should work. If it doesn't make sure you changed the user back to root.
date -s '19 APR 2012 11:14:00'
```bash
master $ kubectl exec -it ubuntu-sleeper -- sh
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
1010         1  0.0  0.0   2512   588 ?        Ss   00:58   0:00 sleep 4800
1010         7  0.0  0.0   2612   604 pts/0    Ss   01:00   0:00 sh
1010        13  0.0  0.0   5892  2852 pts/0    R+   01:00   0:00 ps aux
master $ exit
master $ kubectl exec -it ubuntu-sleeper -- date -s '19 APR 2012 11:14:00'
```