1. How many Deployments exist on the system in the current(default) namespace?

Answer: 1
```bash
master $ kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
frontend-deployment   0/4     4            0           7s
```
2. How many ReplicaSets exist on the system now?

Answer: 1
```bash
master $ kubectl get replicasets.apps
NAME                             DESIRED   CURRENT   READY   AGE
frontend-deployment-66687b8d77   4         4         0       36s
```
3. How many PODs exist on the system now?

Answer: 4
```bash
master $ kubectl get pods
NAME                                   READY   STATUS             RESTARTS   AGE
frontend-deployment-66687b8d77-8vxwp   0/1     ImagePullBackOff   0          83s
frontend-deployment-66687b8d77-blk2g   0/1     ImagePullBackOff   0          83s
frontend-deployment-66687b8d77-hs28g   0/1     ImagePullBackOff   0          83s
frontend-deployment-66687b8d77-nfqp6   0/1     ImagePullBackOff   0          83s
```
4. Out of all the existing PODs, how many are ready?

Answer: 0

5. What is the image used to create the pods in the new deployment?

Answer: busybox888
```bash
master $ kubectl describe pod frontend-deployment-66687b8d77-8vxwp
Name:         frontend-deployment-66687b8d77-8vxwp
Namespace:    default
Priority:     0
Node:         node01/172.17.0.7
Start Time:   Thu, 20 Aug 2020 05:35:35 +0000
Labels:       name=busybox-pod
              pod-template-hash=66687b8d77
Annotations:  <none>
Status:       Pending
IP:           10.244.1.6
IPs:
  IP:           10.244.1.6
Controlled By:  ReplicaSet/frontend-deployment-66687b8d77
Containers:
  busybox-container:
    Container ID:
    Image:         busybox888
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
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-5rm7c (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-5rm7c:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-5rm7c
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                  From               Message
  ----     ------     ----                 ----               -------
  Normal   Scheduled  3m2s                 default-scheduler  Successfully assigned default/frontend-deployment-66687b8d77-8vxwp to node01
  Normal   Pulling    83s (x4 over 2m59s)  kubelet, node01    Pulling image "busybox888"
  Warning  Failed     81s (x4 over 2m57s)  kubelet, node01    Failed to pull image "busybox888": rpc error: code = Unknown desc = Error response from daemon: pull access denied for busybox888, repository does not exist or may require 'docker login':denied: requested access to the resource is denied
  Warning  Failed     81s (x4 over 2m57s)  kubelet, node01    Error: ErrImagePull
  Normal   BackOff    69s (x6 over 2m57s)  kubelet, node01    Back-off pulling image "busybox888"
  Warning  Failed     58s (x7 over 2m57s)  kubelet, node01    Error: ImagePullBackOff
```
6. Why do you think the deployment is not ready?

Answer: repository does not exist

7. Create a new Deployment using the 'deployment-definition-1.yaml' file located at /root/.
There is an issue with the file, so try to fix it. `Name: deployment-1`
```bash
master $ kubectl create -f deployment-definition-1.yaml
Error from server (BadRequest): error when creating "deployment-definition-1.yaml": deployment in version "v1" cannot be handled as a Deployment: no kind "deployment" is registered for version "apps/v1" in scheme "k8s.io/kubernetes/pkg/api/legacyscheme/scheme.go:30"
```
Modify the "kind: deployment" to "kind: Deployment"
```bash
master $ vi deployment-definition-1.yaml
master $ kubectl create -f deployment-definition-1.yaml
deployment.apps/deployment-1 created
```
8. Create a new Deployment with the below attributes using your own deployment definition file
`Name: httpd-frontend`; `Replicas: 3`; `Image: httpd:2.4-alpine`
```bash
master $ cat <<EOF | kubectl apply -f -
> apiVersion: apps/v1
> kind: Deployment
> metadata:
>   name: httpd-frontend
>   labels:
>     app: myapp
>     type: front-end
> spec:
>   template:
>     metadata:
>       name: myapp-pod
>       labels:
>         app: myapp
>         type: front-end
>     spec:
>       containers:
>       - name: httpd-container
>         image: httpd:2.4-alpine
>   replicas: 3
>   selector:
>     matchLabels:
>       type: front-end
> EOF
deployment.apps/httpd-frontend created
```
