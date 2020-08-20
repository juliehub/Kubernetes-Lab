1. How many Namespaces exist on the system?

Answer: 10
```bash
master $ kubectl get namespaces
NAME              STATUS   AGE
default           Active   3m58s
dev               Active   55s
finance           Active   56s
kube-node-lease   Active   4m2s
kube-public       Active   4m2s
kube-system       Active   4m2s
manufacturing     Active   55s
marketing         Active   55s
prod              Active   55s
research          Active   55s
```
2. How many pods exist in the 'research' namespace?

Answer: 2
```bash
master $ kubectl get pods --namespace=research
NAME    READY   STATUS             RESTARTS   AGE
dna-1   0/1     CrashLoopBackOff   3          2m20s
dna-2   0/1     CrashLoopBackOff   3          2m20s
```
3. Create a POD in the 'finance' namespace.
Name: redis
Image Name: redis
```bash
master $ kubectl run redis --image=redis --namespace=finance
pod/redis created
master $ kubectl get pods --namespace=finance
NAME      READY   STATUS    RESTARTS   AGE
payroll   1/1     Running   0          4m1s
redis     1/1     Running   0          12s
```
4. Which namespace has the 'blue' pod in it?

Answer: marketing
```bash
master $ kubectl get pods --all-namespaces
NAMESPACE       NAME                                      READY   STATUS             RESTARTS   AGE
dev             mysql-db                                  1/1     Running            0          6m23s
finance         payroll                                   1/1     Running            0          6m23s
finance         redis                                     1/1     Running            0          2m34s
kube-system     coredns-66bff467f8-7f8mw                  1/1     Running            0          9m10s
kube-system     coredns-66bff467f8-ts594                  1/1     Running            0          9m10s
kube-system     etcd-master                               1/1     Running            0          9m17s
kube-system     katacoda-cloud-provider-58f89f7d9-r6mxh   1/1     Running            5          9m10s
kube-system     kube-apiserver-master                     1/1     Running            0          9m17s
kube-system     kube-controller-manager-master            1/1     Running            0          9m17s
kube-system     kube-flannel-ds-amd64-mkvq5               1/1     Running            0          9m11s
kube-system     kube-flannel-ds-amd64-xc6k5               1/1     Running            0          9m2s
kube-system     kube-keepalived-vip-94nwq                 1/1     Running            0          8m41s
kube-system     kube-proxy-dgb4s                          1/1     Running            0          9m2s
kube-system     kube-proxy-x4chb                          1/1     Running            0          9m11s
kube-system     kube-scheduler-master                     1/1     Running            0          9m17s
manufacturing   red-app                                   1/1     Running            0          6m23s
marketing       blue                                      1/1     Running            0          6m23s
marketing       mysql-db                                  1/1     Running            0          6m23s
research        dna-1                                     0/1     CrashLoopBackOff   5          6m24s
research        dna-2                                     0/1     CrashLoopBackOff   5          6m24s
master $ kubectl get pods --all-namespaces|grep -i blue
marketing       blue                                      1/1     Running            0          6m32s
```
5. Access the Blue web application using the link above your terminal
From the UI you can ping other services.
What DNS name should the Blue application use to access the database 'db-service' in the 'dev' namespace
You can try it in the web application UI. Use port 3306.

Answer: db-service.dev.svc.cluster.local
