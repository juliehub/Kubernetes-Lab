1. In this practice test we will install weave-net POD networking solution to the cluster. Let us first inspect the setup.
We have deployed an application called app in the default namespace. What is the state of the pod?
Answer: Not Running
```bash
controlplane $ kubectl get pods
NAME   READY   STATUS              RESTARTS   AGE
app    0/1     ContainerCreating   0          9m2s
```
2. Inspect why the POD is not running
Answer: No network configured
```bash
controlplane $ kubectl describe pod app
Name:         app
Namespace:    default
Priority:     0Node:         node01/172.17.0.22
Start Time:   Wed, 28 Oct 2020 04:31:12 +0000
Labels:       run=app
Annotations:  <none>
Status:       Pending
IP:
IPs:          <none>
Containers:
  app:
    Container ID:
    Image:         busybox
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Args:
      sleep
      1000
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-wv7s7 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-wv7s7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-wv7s7
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason                  Age                     From               Message
  ----     ------                  ----                    ----               -------
  Normal   Scheduled               9m50s                   default-scheduler  Successfully assigned default/app to node01
  Warning  FailedCreatePodSandBox  9m48s                   kubelet, node01    Failed to create pod sandbox: rpc error: code = Unknown desc = [failed to set up sandbox container "c20d30dbe98e9bc532d4bb235a24034ff8fb6ae093e8eafc53e8fc9cfeedfb34" network for pod "app": networkPlugin cni failed to set up pod "app_default" network: unable to allocate IP address: Post "http://127.0.0.1:6784/ip/c20d30dbe98e9bc532d4bb235a24034ff8fb6ae093e8eafc53e8fc9cfeedfb34": dial tcp 127.0.0.1:6784: connect: connection refused, failed to clean up sandbox container "c20d30dbe98e9bc532d4bb235a24034ff8fb6ae093e8eafc53e8fc9cfeedfb34" network for pod "app": networkPlugin cni failed to teardown pod "app_default" network: Delete "http://127.0.0.1:6784/ip/c20d30dbe98e9bc532d4bb235a24034ff8fb6ae093e8eafc53e8fc9cfeedfb34": dial tcp 127.0.0.1:6784: connect: connection refused]
  Normal   SandboxChanged          4m43s (x26 over 9m47s)  kubelet, node01    Pod sandbox changed, it will be killed and re-created.
```
No network plugin was configured
```bash
controlplane $ kubectl -n kube-system get pods
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-f9fd979d6-dsnm7                1/1     Running   0          25m
coredns-f9fd979d6-mwlz7                1/1     Running   0          25m
etcd-controlplane                      1/1     Running   0          49m
kube-apiserver-controlplane            1/1     Running   0          49m
kube-controller-manager-controlplane   1/1     Running   0          49m
kube-proxy-vft9v                       1/1     Running   0          48m
kube-proxy-zrtrm                       1/1     Running   0          48m
kube-scheduler-controlplane            1/1     Running   0          49m
```
3. Deploy `weave-net` networking solution to the cluster
Check the documentation here: https://www.weave.works/docs/net/latest/kubernetes/kube-addon/
```bash
controlplane $ kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
serviceaccount/weave-net created
clusterrole.rbac.authorization.k8s.io/weave-net created
clusterrolebinding.rbac.authorization.k8s.io/weave-net created
role.rbac.authorization.k8s.io/weave-net created
rolebinding.rbac.authorization.k8s.io/weave-net created
daemonset.apps/weave-net created
```