1. This lab tests your skills on upgrading a kubernetes cluster. We have a production cluster with applications running on it. Let us explore the setup first.
What is the current version of the cluster? v1.17.0
```bash
master $ kubectl get nodes
NAME     STATUS   ROLES    AGE    VERSION
master   Ready    master   130m   v1.17.0
node01   Ready    <none>   129m   v1.17.0
master $ kubectl version --short
Client Version: v1.17.0
Server Version: v1.17.0
```
2. How many nodes are part of this cluster? 2
Including master and worker nodes

3. How many nodes can host workloads in this cluster? 2
Inspect the applications
```bash
master $ kubectl get pods -o wide
NAME                   READY   STATUS    RESTARTS   AGE    IP          NODE     NOMINATED NODE   READINESS GATES
red-5dc59c9654-8p8v8   1/1     Running   0          7m3s   10.44.0.3   node01   <none>           <none>
red-5dc59c9654-chkxl   1/1     Running   0          7m3s   10.32.0.2   master   <none>           <none>
```

4. How many applications are hosted on the cluster? 1
Count the number of deployments
```bash
master $ kubectl get deployments.apps
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
red    2/2     2            2           8m22s
```

5. What nodes are the pods hosted on? master and node01

6. You are tasked to upgrade the cluster. User's accessing the applications must not be impacted. And you cannot provision new VMs. What strategy would you use to upgrade the cluster?
Upgrade one node at a time while moving the workloads to the other.

7. What is the latest stable version available for upgrade? v1.17.12
```bash
master $ kubeadm upgrade plan
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[preflight] Running pre-flight checks.
[upgrade] Making sure the cluster is healthy:
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: v1.17.0
[upgrade/versions] kubeadm version: v1.17.0
I0923 00:45:06.691410    3005 version.go:251] remote version is much newer: v1.19.2; falling back to: stable-1.17
[upgrade/versions] Latest stable version: v1.17.12
[upgrade/versions] Latest version in the v1.17 series: v1.17.12

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   CURRENT       AVAILABLE
Kubelet     2 x v1.17.0   v1.17.12

Upgrade to the latest version in the v1.17 series:

COMPONENT            CURRENT   AVAILABLE
API Server           v1.17.0   v1.17.12
Controller Manager   v1.17.0   v1.17.12
Scheduler            v1.17.0   v1.17.12
Kube Proxy           v1.17.0   v1.17.12
CoreDNS              1.6.5     1.6.5
Etcd                 3.4.3     3.4.3-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.17.12

Note: Before you can perform this upgrade, you have to update kubeadm to v1.17.12.
```
8. We will be upgrading the master node first. Drain the master node of workloads and mark it `UnSchedulable`
- Master Node: SchedulingDisabled
```bash
master $ kubectl drain master
node/master cordoned
evicting pod "red-5dc59c9654-chkxl"
pod/red-5dc59c9654-chkxl evicted
node/master evicted
```
9. Upgrade the master/controlplane components to exact version v1.18.0
Upgrade kubeadm tool (if not already), then the master components, and finally the kubelet. Practice referring to the kubernetes documentation page. Note: While upgrading kubelet, if you hit dependency issue while running the apt-get upgrade kubelet command, use the apt install kubelet=1.18.0-00 command instead
- Master Upgraded to v1.18.0
- Master Kubelet Upgraded to v1.18.0
```bash
master $ apt install kubeadm=1.18.0-00
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following package was automatically installed and is no longer required:
  libuv1
Use 'apt autoremove' to remove it.
The following held packages will be changed:
  kubeadm
The following packages will be upgraded:
  kubeadm
```
```bash
master $ apt install kubeadm=1.18.0-00
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following package was automatically installed and is no longer required:
  libuv1
Use 'apt autoremove' to remove it.
The following held packages will be changed:
  kubeadm
The following packages will be upgraded:
  kubeadm
1 to upgrade, 0 to newly install, 0 to remove and 362 not to upgrade.
Need to get 8,163 kB of archives.
After this operation, 467 kB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.18.0-00 [8,163 kB]
Fetched 8,163 kB in 0s (17.5 MB/s)
(Reading database ... 248896 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.18.0-00_amd64.deb ...
Unpacking kubeadm (1.18.0-00) over (1.17.0-00) ...
Setting up kubeadm (1.18.0-00) ...
master $ kubeadm upgrade apply v1.18.0
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade/version] You have chosen to change the cluster version to "v1.18.0"
[upgrade/versions] Cluster version: v1.17.0
[upgrade/versions] kubeadm version: v1.18.0
[upgrade/confirm] Are you sure you want to proceed with the upgrade? [y/N]: y
[upgrade/prepull] Will prepull images for components [kube-apiserver kube-controller-manager kube-scheduler etcd]
[upgrade/prepull] Prepulling image for component etcd.
[upgrade/prepull] Prepulling image for component kube-apiserver.
[upgrade/prepull] Prepulling image for component kube-controller-manager.
[upgrade/prepull] Prepulling image for component kube-scheduler.
[apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
[apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-etcd
[apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-apiserver
[apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-kube-controller-manager
[apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
[apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-etcd
[apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-controller-manager
[upgrade/prepull] Prepulled image for component etcd.
[upgrade/prepull] Prepulled image for component kube-apiserver.
[upgrade/prepull] Prepulled image for component kube-scheduler.
[upgrade/prepull] Prepulled image for component kube-controller-manager.
[upgrade/prepull] Successfully prepulled the images for all the control plane components
[upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.18.0"...
Static pod: kube-apiserver-master hash: 9d0f603dbb7c526dba76e127fe1d4070
Static pod: kube-controller-manager-master hash: e7bc401dca2029dd58a272da618fc389
Static pod: kube-scheduler-master hash: ff67867321338ffd885039e188f6b424
[upgrade/etcd] Upgrading to TLS for etcd
[upgrade/etcd] Non fatal issue encountered during upgrade: the desired etcd version for this Kubernetes version "v1.18.0" is"3.4.3-0", but the current etcd version is "3.4.3". Won't downgrade etcd, instead just continue
[upgrade/staticpods] Writing new Static Pod manifests to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests991274615"
W0923 00:53:05.307344    4469 manifests.go:225] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[upgrade/staticpods] Preparing for "kube-apiserver" upgrade
[upgrade/staticpods] Renewing apiserver certificate
[upgrade/staticpods] Renewing apiserver-kubelet-client certificate
[upgrade/staticpods] Renewing front-proxy-client certificate
[upgrade/staticpods] Renewing apiserver-etcd-client certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-apiserver.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2020-09-23-00-53-04/kube-apiserver.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
Static pod: kube-apiserver-master hash: 9d0f603dbb7c526dba76e127fe1d4070
Static pod: kube-apiserver-master hash: 6cad032fb228c583d618da270e1deb2b
[apiclient] Found 1 Pods for label selector component=kube-apiserver
[upgrade/staticpods] Component "kube-apiserver" upgraded successfully!
[upgrade/staticpods] Preparing for "kube-controller-manager" upgrade
[upgrade/staticpods] Renewing controller-manager.conf certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-controller-manager.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2020-09-23-00-53-04/kube-controller-manager.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
Static pod: kube-controller-manager-master hash: e7bc401dca2029dd58a272da618fc389
Static pod: kube-controller-manager-master hash: 9dd0791647edb90399ea517209b158d4
[apiclient] Found 1 Pods for label selector component=kube-controller-manager
[upgrade/staticpods] Component "kube-controller-manager" upgraded successfully!
[upgrade/staticpods] Preparing for "kube-scheduler" upgrade
[upgrade/staticpods] Renewing scheduler.conf certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-scheduler.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2020-09-23-00-53-04/kube-scheduler.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
Static pod: kube-scheduler-master hash: ff67867321338ffd885039e188f6b424
Static pod: kube-scheduler-master hash: 68835a2012b9716a7c018f4247ae940d
[apiclient] Found 1 Pods for label selector component=kube-scheduler
[upgrade/staticpods] Component "kube-scheduler" upgraded successfully!
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.18" in namespace kube-system with the configuration for the kubelets in thecluster
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.18" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node BootstrapToken
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.18.0". Enjoy!

[upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.
master $ kubectl version --short
Client Version: v1.17.0
Server Version: v1.18.0
master $ kubectl get nodes
NAME     STATUS                     ROLES    AGE    VERSION
master   Ready,SchedulingDisabled   master   139m   v1.17.0
node01   Ready                      <none>   138m   v1.17.0
```
kubelet has not been upgrade, so `kubectl get nodes` still shows old version although the cluster has been upgraded.
```bash
master $ apt install kubelet=1.18.0-00
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following package was automatically installed and is no longer required:
  libuv1
Use 'apt autoremove' to remove it.
The following packages will be upgraded:
  kubelet
1 to upgrade, 0 to newly install, 0 to remove and 362 not to upgrade.
Need to get 19.4 MB of archives.
After this operation, 1,696 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.18.0-00 [19.4 MB]
Fetched 19.4 MB in 2s (8,908 kB/s)
(Reading database ... 248896 files and directories currently installed.)
Preparing to unpack .../kubelet_1.18.0-00_amd64.deb ...
Unpacking kubelet (1.18.0-00) over (1.17.0-00) ...
Setting up kubelet (1.18.0-00) ...
master $ kubectl version --short
Client Version: v1.17.0
Server Version: v1.18.0
NAME     STATUS                     ROLES    AGE    VERSION
master   Ready,SchedulingDisabled   master   141m   v1.18.0
node01   Ready                      <none>   141m   v1.17.0
```
9. Mark the master/controlplane node as "Schedulable" again
```bash
master $ kubectl uncordon master
node/master uncordoned
```
10. Next is the worker node. Drain the worker node of the workloads and mark it UnSchedulable
```bash
master $ kubectl drain node01
node/node01 cordoned
evicting pod "red-5dc59c9654-xf6hw"
evicting pod "red-5dc59c9654-8p8v8"
evicting pod "coredns-66bff467f8-2s6ww"
evicting pod "coredns-66bff467f8-lj7dw"
pod/coredns-66bff467f8-lj7dw evicted
pod/coredns-66bff467f8-2s6ww evicted
pod/red-5dc59c9654-xf6hw evicted
pod/red-5dc59c9654-8p8v8 evicted
node/node01 evicted
```
11. Upgrade the worker node to the exact version v1.18.0
- Worker Node Upgraded to v1.18.0
- Worker Node Ready
```bash
master $ kubectl get nodes
NAME     STATUS                     ROLES    AGE    VERSION
master   Ready                      master   144m   v1.18.0
node01   Ready,SchedulingDisabled   <none>   144m   v1.17.0
master $ ssh node01
node01 $ apt install kubeadm=1.18.0-00
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following package was automatically installed and is no longer required:
  libuv1
Use 'apt autoremove' to remove it.
The following packages will be upgraded:
  kubeadm
1 to upgrade, 0 to newly install, 0 to remove and 361 not to upgrade.
Need to get 8,163 kB of archives.
After this operation, 467 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.18.0-00 [8,163 kB]
Fetched 8,163 kB in 0s (15.7 MB/s)
(Reading database ... 248903 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.18.0-00_amd64.deb ...
Unpacking kubeadm (1.18.0-00) over (1.17.0-00) ...
Setting up kubeadm (1.18.0-00) ...
```
```bash
node01 $ kubeadm upgrade node
[upgrade] Reading configuration from the cluster...
[upgrade] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[upgrade] Skipping phase. Not a control plane node.
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.18" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[upgrade] The configuration for this node was successfully updated!
[upgrade] Now you should go ahead and upgrade the kubelet package using your package manager.
```
```bash
node01 $ apt install kubelet=1.18.0-00
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following package was automatically installed and is no longer required:
  libuv1
Use 'apt autoremove' to remove it.
The following packages will be upgraded:
  kubelet
1 to upgrade, 0 to newly install, 0 to remove and 361 not to upgrade.
Need to get 19.4 MB of archives.
After this operation, 1,696 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.18.0-00 [19.4 MB]
Fetched 19.4 MB in 1s (11.6 MB/s)
(Reading database ... 248903 files and directories currently installed.)
Preparing to unpack .../kubelet_1.18.0-00_amd64.deb ...
Unpacking kubelet (1.18.0-00) over (1.17.0-00) ...
Setting up kubelet (1.18.0-00) ...
node01 $ exit
logout
Connection to node01 closed.
```
```bash
master $ kubectl get nodes
NAME     STATUS                     ROLES    AGE    VERSION
master   Ready                      master   147m   v1.18.0
node01   Ready,SchedulingDisabled   <none>   147m   v1.18.0
```
12. Remove the restriction and mark the worker node as schedulable again.
```bash
master $ kubectl uncordon node01
node/node01 uncordoned
```

