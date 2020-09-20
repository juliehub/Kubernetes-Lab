1. What is the name of the POD that deploys the default kubernetes scheduler in this environment? kube-scheduler-master
```bash
master $ kubectl get pods --namespace=kube-system
NAME                                      READY   STATUS    RESTARTS   AGE
coredns-66bff467f8-4vdw8                  1/1     Running   0          102s
coredns-66bff467f8-prmlq                  1/1     Running   0          102s
etcd-master                               1/1     Running   0          110s
katacoda-cloud-provider-58f89f7d9-9hj5l   1/1     Running   0          102s
kube-apiserver-master                     1/1     Running   0          110s
kube-controller-manager-master            1/1     Running   0          110s
kube-flannel-ds-amd64-4cs24               1/1     Running   0          102s
kube-flannel-ds-amd64-hjxdz               1/1     Running   0          93s
kube-keepalived-vip-c74fs                 1/1     Running   0          72s
kube-proxy-7jshv                          1/1     Running   0          102s
kube-proxy-tq7vl                          1/1     Running   0          93s
kube-scheduler-master                     1/1     Running   0          110s
```
2. What is the image used to deploy the kubernetes scheduler?
Inspect the kubernetes scheduler pod and identify the image
```bash
master $ kubectl describe pod kube-scheduler-master --namespace=kube-system | grep -i image
    Image:         k8s.gcr.io/kube-scheduler:v1.18.0
    Image ID:      docker-pullable://k8s.gcr.io/kube-scheduler@sha256:33063bc856e99d12b9cb30aab1c1c755ecd458d5bd130270da7c51c70ca10cf6
```
3. Deploy an additional scheduler to the cluster following the given specification
- Namespace: kube-system
- Name: my-scheduler
- Status: Running
- Custom Scheduler Name
Use the manifest file used by kubeadm tool. Use a different port than the one used by the current one.
```bash
master $ cd /etc/kubernetes/manifests/
master $ cp kube-scheduler.yaml /root/
master $ cd /root
master $ mv kube-scheduler.yaml my-scheduler.yaml
master $ vi my-scheduler.yaml
master $ cat my-scheduler.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-scheduler
    tier: control-plane
  name: my-scheduler
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-scheduler
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=false
    - --scheduler-name=my-scheduler
    image: k8s.gcr.io/kube-scheduler:v1.18.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 127.0.0.1
        path: /healthz
        port: 10259
        scheme: HTTPS
      initialDelaySeconds: 15
      timeoutSeconds: 15
    name: my-scheduler
    resources:
      requests:
        cpu: 100m
    volumeMounts:
    - mountPath: /etc/kubernetes/scheduler.conf
      name: kubeconfig
      readOnly: true
  hostNetwork: true
  priorityClassName: system-cluster-critical
  volumes:
  - hostPath:
      path: /etc/kubernetes/scheduler.conf
      type: FileOrCreate
    name: kubeconfig
status: {}
master $ kubectl -n kube-system get pod
NAME                                      READY   STATUS             RESTARTS   AGE
coredns-66bff467f8-4vdw8                  1/1     Running            0          17m
coredns-66bff467f8-prmlq                  1/1     Running            0          17m
etcd-master                               1/1     Running            0          17m
katacoda-cloud-provider-58f89f7d9-9hj5l   0/1     CrashLoopBackOff   7          17m
kube-apiserver-master                     1/1     Running            0          17m
kube-controller-manager-master            1/1     Running            0          17m
kube-flannel-ds-amd64-4cs24               1/1     Running            0          17m
kube-flannel-ds-amd64-hjxdz               1/1     Running            0          17m
kube-keepalived-vip-c74fs                 1/1     Running            0          17m
kube-proxy-7jshv                          1/1     Running            0          17m
kube-proxy-tq7vl                          1/1     Running            0          17m
kube-scheduler-master                     1/1     Running            0          17m
my-scheduler                              1/1     Running            0          35s
```
4. A POD definition file is given. Use it to create a POD with the new custom scheduler.
File is located at /root/nginx-pod.yaml
```bash
master $ cat nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginxmaster $
master $ vi nginx-pod.yaml
master $ cat nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  schedulerName: my-scheduler
  containers:
  -  image: nginx
     name: nginx
master $ kubectl create -f nginx-pod.yaml
pod/nginx created
master $ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          16s
master $ kubectl describe pod nginx
Name:         nginx
Namespace:    default
Priority:     0
Node:         node01/172.17.0.46
Start Time:   Fri, 18 Sep 2020 03:53:00 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.244.1.3
IPs:
  IP:  10.244.1.3
Containers:
  nginx:
    Container ID:   docker://be30dde7d7e1e6dced6486dbf3040a02c59e973fe495a6bef02eafe08fe710eb
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:c628b67d21744fce822d22fdcc0389f6bd763daac23a6b77147d0712ea7102d0
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 18 Sep 2020 03:53:07 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-7sjvd (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-7sjvd:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-7sjvd
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From             Message
  ----    ------     ----       ----             -------
  Normal  Scheduled  <unknown>  my-scheduler     Successfully assigned default/nginx to node01
  Normal  Pulling    75s        kubelet, node01  Pulling image "nginx"
  Normal  Pulled     69s        kubelet, node01  Successfully pulled image "nginx"
  Normal  Created    69s        kubelet, node01  Created container nginx
  Normal  Started    69s        kubelet, node01  Started container nginx
```
