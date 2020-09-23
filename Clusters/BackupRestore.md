1. We have a working kubernetes cluster with a set of applications running. Let us first explore the setup.
How many deployments exist in the cluster? 2
```bash
master $ kubectl get deployment
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
blue   3/3     3            3           5m8s
red    2/2     2            2           5m8s
```
2. What is the version of ETCD running on the cluster? 3.4.3-0
Check the ETCD Pod or Process
```bash
master $ kubectl get pods -n kube-system
NAME                                      READY   STATUS    RESTARTS   AGE
coredns-66bff467f8-hvh4x                  1/1     Running   0          8m45s
coredns-66bff467f8-zp8kz                  1/1     Running   0          8m44s
etcd-controlplane                         1/1     Running   0          8m54s
katacoda-cloud-provider-58f89f7d9-btm4s   1/1     Running   5          8m45s
kube-apiserver-controlplane               1/1     Running   0          8m54s
kube-controller-manager-controlplane      1/1     Running   0          8m54s
kube-flannel-ds-amd64-4vnnj               1/1     Running   0          8m45s
kube-flannel-ds-amd64-rv79w               1/1     Running   1          8m35s
kube-keepalived-vip-6dbr8                 1/1     Running   0          8m24s
kube-proxy-c2pgv                          1/1     Running   0          8m45s
kube-proxy-xt9c4                          1/1     Running   0          8m35s
kube-scheduler-controlplane               1/1     Running   0          8m54s
master $ kubectl describe pod etcd-controlplane -n kube-system | grep -iA5 image
    Image:         k8s.gcr.io/etcd:3.4.3-0
    Image ID:      docker-pullable://k8s.gcr.io/etcd@sha256:4afb99b4690b418ffc2ceb67e1a17376457e441c1f09ab55447f0aaf992fa646
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --advertise-client-urls=https://172.17.0.10:2379
```
3.  At what address do you reach the ETCD cluster from your master node?
Check the ETCD Service configuration in the ETCD POD
https://172.17.0.10:2379

4. Where is the ETCD server certificate file located?
Note this path down as you will need to use it later
--cert-file=/etc/kubernetes/pki/etcd/server.crt

5. Where is the ETCD CA Certificate file located?
Note this path down as you will need to use it later
-peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt

6. The master nodes in our cluster are planned for a regular maintenance reboot tonight. While we do not anticipate anything to go wrong, we are required to take the necessary backups. Take a snapshot of the ETCD database using the built-in snapshot functionality.
Store the backup file at location /tmp/snapshot-pre-boot.d
- Backup ETCD to /tmp/snapshot-pre-boot.db
```bash
master $ ETCDCTL_API=3 etcdctl snapshot save -h
NAME:
        snapshot save - Stores an etcd node backend snapshot to a given file

USAGE:
        etcdctl snapshot save <filename>

GLOBAL OPTIONS:
      --cacert=""                               verify certificates of TLS-enabled secure servers using this CA bundle
      --cert=""                                 identify secure client using this TLS certificate file
      --command-timeout=5s                      timeout for short running command (excluding dial timeout)
      --debug[=false]                           enable client-side debug logging
      --dial-timeout=2s                         dial timeout for client connections
  -d, --discovery-srv=""                        domain name to query for SRV records describing cluster endpoints
      --endpoints=[127.0.0.1:2379]              gRPC endpoints
      --hex[=false]                             print byte strings as hex encoded strings
      --insecure-discovery[=true]               accept insecure SRV records describing cluster endpoints
      --insecure-skip-tls-verify[=false]        skip server certificate verification
      --insecure-transport[=true]               disable transport security for client connections
      --keepalive-time=2s                       keepalive time for client connections
      --keepalive-timeout=6s                    keepalive timeout for client connections
      --key=""                                  identify secure client using this TLS key file
      --user=""                                 username[:password] for authentication (prompt if password is not supplied)
  -w, --write-out="simple"                      set the output format (fields, json, protobuf, simple, table)

```
```bash
master $ ETCDCTL_API=3 etcdctl member list --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key --endpoints=https://[127.0.0.1]:2379
f2872e5e711415ed, started, controlplane, https://172.17.0.10:2380, https://172.17.0.10:2379
master $ ETCDCTL_API=3 etcdctl snapshot save --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key --endpoints=https://[127.0.0.1]:2379 /tmp/snapshot-pre-boot.db
Snapshot saved at /tmp/snapshot-pre-boot.db
```
7. Great! Let us now wait for the maintenance window to finish. Go get some sleep. (Don't go for real)
Click Ok to Continue
```bash
master $ kubectl get node
NAME           STATUS   ROLES    AGE   VERSION
controlplane   Ready    master   41m   v1.18.0
node01         Ready    <none>   40m   v1.18.0
```
8. Wake up! We have a conference call! After the reboot the master nodes came back online, but none of our applications are accessible. Check the status of the applications on the cluster. What's wrong?
Deployments, Services, Pods all not available
```bash
master $ kubectl get pods,deployments,svc
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   31s
```
9. Luckily we took a backup. Restore the original state of the cluster using the backup file.
- Deployments: 2
- Services: 3
```bash
master $ ETCDCTL_API=3 etcdctl snapshot restore -h
NAME:
        snapshot restore - Restores an etcd member snapshot to an etcd directory

USAGE:
        etcdctl snapshot restore <filename> [options]

OPTIONS:
      --data-dir=""                                             Path to the data directory
      --initial-advertise-peer-urls="http://localhost:2380"     List of this member's peer URLs to advertise to the rest of the cluster
      --initial-cluster="default=http://localhost:2380"         Initial cluster configuration for restore bootstrap
      --initial-cluster-token="etcd-cluster"                    Initial cluster token for the etcd cluster during restore bootstrap
      --name="default"                                          Human-readable name for this member
      --skip-hash-check[=false]                                 Ignore snapshot integrity hash value (required if copied from data directory)
      --wal-dir=""                                              Path to the WAL directory (use --data-dir if none given)

GLOBAL OPTIONS:
      --cacert=""                               verify certificates of TLS-enabled secure servers using this CA bundle
      --cert=""                                 identify secure client using this TLS certificate file
      --command-timeout=5s                      timeout for short running command (excluding dial timeout)
      --debug[=false]                           enable client-side debug logging
      --dial-timeout=2s                         dial timeout for client connections
  -d, --discovery-srv=""                        domain name to query for SRV records describing cluster endpoints
      --endpoints=[127.0.0.1:2379]              gRPC endpoints
      --hex[=false]                             print byte strings as hex encoded strings
      --insecure-discovery[=true]               accept insecure SRV records describing cluster endpoints
      --insecure-skip-tls-verify[=false]        skip server certificate verification
      --insecure-transport[=true]               disable transport security for client connections
      --keepalive-time=2s                       keepalive time for client connections
      --keepalive-timeout=6s                    keepalive timeout for client connections
      --key=""                                  identify secure client using this TLS key file
      --user=""                                 username[:password] for authentication (prompt if password is not supplied)
  -w, --write-out="simple"                      set the output format (fields, json, protobuf, simple, table)
```
```bash
master $ ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
>      --name=master \
>      --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key \
>      --data-dir /var/lib/etcd-from-backup \
>      --initial-cluster=master=https://127.0.0.1:2380 \
>      --initial-cluster-token=etcd-cluster-1 \
>      --initial-advertise-peer-urls=https://127.0.0.1:2380 \
>      snapshot restore /tmp/snapshot-pre-boot.db
2020-09-23 04:04:21.229013 I | mvcc: restore compact to 5161
2020-09-23 04:04:21.272459 I | etcdserver/membership: added member e92d66acd89ecf29 [https://127.0.0.1:2380] to cluster 7581d6eb2d25405b
```
```bash
master $ ls -l /var/lib/etcd-from-backup/
total 4
drwx------ 4 root root 4096 Sep 23 05:53 member
```
Update using [this guide](https://github.com/mmumshad/kubernetes-the-hard-way/blob/master/practice-questions-answers/cluster-maintenance/backup-etcd/etcd-backup-and-restore.md)
```bash
aster $ docker ps -a | grep etcd
6b5cfebfbc3f        303ce5db0e90             "etcd --advertise-cl…"   11 seconds ago      Up 11 seconds               k8s_etcd_etcd-master_kube-system_710fc0844ca48b2ab8862fcbe58c6bf5_0
766c34fd6251        k8s.gcr.io/pause:3.2     "/pause"                 12 seconds ago      Up 12 seconds               k8s_POD_etcd-master_kube-system_710fc0844ca48b2ab8862fcbe58c6bf5_0
master $ ETCDCTL_API=3 etcdctl member list --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key --endpoints=https://[127.0.0.1]:2379
e92d66acd89ecf29, started, master, https://127.0.0.1:2380, https://172.17.0.19:2379
master $ kubectl get pods,deployment,svcNAME                        READY   STATUS    RESTARTS   AGE
pod/blue-8455cd8cd7-57thd   1/1     Running   0          42m
pod/blue-8455cd8cd7-ncvwz   1/1     Running   0          42m
pod/blue-8455cd8cd7-qqw2k   1/1     Running   0          42m
pod/red-59d898f784-8gzb7    1/1     Running   0          42m
pod/red-59d898f784-bdmpn    1/1     Running   0          42m

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/blue   3/3     3            3           42m
deployment.apps/red    2/2     2            2           42m

NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/blue-service   NodePort    10.100.95.179   <none>        80:30082/TCP   42m
service/kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP        133m
service/red-service    NodePort    10.110.154.72   <none>        80:30080/TCP   42m
```
