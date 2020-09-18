1. Deploy the metrics-server by creating all the components downloaded
```bash
master $ git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
Cloning into 'kubernetes-metrics-server'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 15 (delta 0), reused 0 (delta 0), pack-reused 12
Unpacking objects: 100% (15/15), done.
master $ cd kubernetes-metrics-server/
master $ ls
aggregated-metrics-reader.yaml  auth-reader.yaml         metrics-server-deployment.yaml  README.md
auth-delegator.yaml             metrics-apiservice.yaml  metrics-server-service.yaml     resource-reader.yaml
master $ kubectl create -f .
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
serviceaccount/metrics-server created
deployment.apps/metrics-server created
service/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
```
2. It takes a few minutes for the metrics server to start gathering data.
```bash
master $ kubectl top node
NAME     CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
master   158m         7%     1119Mi          59%
node01   2000m        100%   831Mi           21%
```
3. Identify the POD that consumes the most Memory. elephant
```bash
master $ kubectl top pod
NAME       CPU(cores)   MEMORY(bytes)
elephant   8m           10Mi
lion       934m         1Mi
rabbit     958m         1Mi
```
