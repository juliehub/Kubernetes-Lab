1. How many static pods exist in this cluster in all namespaces? 4
Hint: Run the command kubectl get pods --all-namespaces and look for those with -master appended in the name
```bash
master $ kubectl get pods --all-namespaces
NAMESPACE     NAME                                      READY   STATUS             RESTARTS   AGE
kube-system   coredns-66bff467f8-2ldpj                  1/1     Running            0          17m
kube-system   coredns-66bff467f8-dhw4l                  1/1     Running            0          17m
kube-system   etcd-master                               1/1     Running            0          17m
kube-system   katacoda-cloud-provider-58f89f7d9-rnpjv   0/1     CrashLoopBackOff   7          17m
kube-system   kube-apiserver-master                     1/1     Running            0          17m
kube-system   kube-controller-manager-master            1/1     Running            0          17m
kube-system   kube-flannel-ds-amd64-xmzjj               1/1     Running            1          17m
kube-system   kube-flannel-ds-amd64-ztswm               1/1     Running            0          17m
kube-system   kube-keepalived-vip-wq5f2                 1/1     Running            0          17m
kube-system   kube-proxy-7vlfd                          1/1     Running            0          17m
kube-system   kube-proxy-kbtzt                          1/1     Running            0          17m
kube-system   kube-scheduler-master                     1/1     Running            0          17m
```
2. On what nodes are the static pods created? master
```bash
master $ kubectl get pods --all-namespaces -o wide |grep master
kube-system   coredns-66bff467f8-2ldpj                  1/1     Running            0          28m   10.244.0.3    master   <none>           <none>
kube-system   coredns-66bff467f8-dhw4l                  1/1     Running            0          28m   10.244.0.2    master   <none>           <none>
kube-system   etcd-master                               1/1     Running            0          28m   172.17.0.9    master   <none>           <none>
kube-system   kube-apiserver-master                     1/1     Running            0          28m   172.17.0.9    master   <none>           <none>
kube-system   kube-controller-manager-master            1/1     Running            0          28m   172.17.0.9    master   <none>           <none>
kube-system   kube-flannel-ds-amd64-ztswm               1/1     Running            0          28m   172.17.0.9    master   <none>           <none>
kube-system   kube-proxy-kbtzt                          1/1     Running            0          28m   172.17.0.9    master   <none>           <none>
kube-system   kube-scheduler-master                     1/1     Running            0          28m   172.17.0.9    master   <none>           <none>
```
3. What is the path of the directory holding the static pod definition files?
staticPodPath: `/etc/kubernetes/manifests`
```bash
master $ ps -aux | grep kubelet
root      3202  2.8  4.4 1353756 90700 ?       Ssl  Sep17   0:53 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf
--kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --cgroup-driver=systemd --network-plugin=cni
--pod-infra-container-image=k8s.gcr.io/pause:3.2 --resolv-conf=/run/systemd/resolve/resolv.conf

master $ cat /var/lib/kubelet/config.yaml
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 0s
    cacheUnauthorizedTTL: 0s
clusterDNS:
- 10.96.0.10
clusterDomain: cluster.local
cpuManagerReconcilePeriod: 0s
evictionPressureTransitionPeriod: 0s
fileCheckFrequency: 0s
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 0s
imageMinimumGCAge: 0s
kind: KubeletConfiguration
nodeStatusReportFrequency: 0s
nodeStatusUpdateFrequency: 0s
rotateCertificates: true
runtimeRequestTimeout: 0s
staticPodPath: /etc/kubernetes/manifests
streamingConnectionIdleTimeout: 0s
syncFrequency: 0s
volumeStatsAggPeriod: 0s
```
or 
```bash
$ grep -i static /var/lib/kubelet/config.yaml
staticPodPath: /etc/kubernetes/manifests
```
4. How many pod definition files are present in the manifests folder? 4
```bash
master $ ls -l /etc/kubernetes/manifests
total 16
-rw------- 1 root root 1832 Sep 17 23:31 etcd.yaml
-rw------- 1 root root 3366 Sep 17 23:31 kube-apiserver.yaml
-rw------- 1 root root 3231 Sep 17 23:31 kube-controller-manager.yaml
-rw------- 1 root root 1120 Sep 17 23:31 kube-scheduler.yaml
```
5. Create a static pod named static-busybox that uses the busybox image and the command sleep 1000
```bash
master $ kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > static-busybox.yaml
master $ cat static-busybox.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: static-busybox
  name: static-busybox
spec:
  containers:
  - command:
    - sleep
    - "1000"
    image: busybox
    name: static-busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
master $ kubectl get pods -o wide
NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE     NOMINATED NODE   READINESS GATES
static-busybox          1/1     Running   0          13s     10.244.1.3   node01   <none>           <none>
static-busybox-master   1/1     Running   0          2m37s   10.244.0.4   master   <none>           <none>
```
6. Edit the image on the static pod to use busybox:1.28.4
Simply edit the static pod definition file and save it. If that does not re-create the pod,
run: 'kubectl run --restart=Never --image=busybox:1.28.4 static-busybox --dry-run=client -o yaml --command -- sleep 1000
> /etc/kubernetes/manifests/static-busybox.yaml'
```bash
master $ kubectl delete pod static-busybox
pod "static-busybox" deleted
master $ kubectl create -f static-busybox.yaml
pod/static-busybox created
master $ kubectl describe pod static-busybox|grep image
  Normal  Pulling    9s    kubelet, node01    Pulling image "busybox:1.28.4"
  Normal  Pulled     7s    kubelet, node01    Successfully pulled image "busybox:1.28.4"
```
