1. How many ClusterRoles do you see defined in the cluster?
```bash
master $ kubectl get clusterroles --no-headers | wc -l
59
```
2. How many ClusterRoleBindings exist on the cluster?
```bash
master $ kubectl get clusterrolebindings --no-headers | wc -l
45
```
3. What namespace is the cluster-admin clusterrole part of?
Cluster Roles are cluster wide and not part of any namespace.
```bash
master $ kubectl describe clusterrole cluster-admin
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----  *.*        []                 []              [*]
             [*]                []              [*]
```
4. What user/groups are the cluster-admin role bound to? `system:masters`
The ClusterRoleBinding for the role is with the same name.
```bash
master $ kubectl describe clusterrolebinding cluster-admin
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
Role:
  Kind:  ClusterRole
  Name:  cluster-admin
Subjects:
  Kind   Name            Namespace
  ----   ----            ---------
  Group  system:masters
```
5. What level of permission does the cluster-admin role grant?
Inspect the cluster-admin role's privileges
Answer: Perform any action on any resource in the cluster
```bash
master $ kubectl describe clusterrole cluster-admin
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  *.*        []                 []              [*]
             [*]                []              [*]
```
6. A new user michelle joined the team. She will be focusing on the nodes in the cluster. Create the required ClusterRoles and ClusterRoleBindings so she gets access to the nodes.
```bash
master $ cat michelle-node-admin.yaml
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: node-admin
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "watch", "list", "create", "delete"]

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: michelle-binding
subjects:
- kind: User
  name: michelle
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: node-admin
  apiGroup: rbac.authorization.k8s.io
master $ kubectl create -f michelle-node-admin.yaml
clusterrole.rbac.authorization.k8s.io/node-admin created
clusterrolebinding.rbac.authorization.k8s.io/michelle-binding created
```
7. michelle's responsibilities are growing and now she will be responsible for storage as well. Create the required ClusterRoles and ClusterRoleBindings to allow her access to Storage.
Get the API groups and resource names from command kubectl api-resources. Use the given spec.
- ClusterRole: storage-admin
- Resource: persistentvolumes
- Resource: storageclasses
- ClusterRoleBinding: michelle-storage-admin
- ClusterRoleBinding Subject: michelle
- ClusterRoleBinding Role: storage-admin
```bash
master $ kubectl create -f michelle-storage-admin.yaml
clusterrole.rbac.authorization.k8s.io/storage-admin created
clusterrolebinding.rbac.authorization.k8s.io/michelle-storage-admin created
master $ cat michelle-storage-admin.yaml
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: storage-admin
rules:
- apiGroups: [""]
  resources: ["persistentvolumes"]
  verbs: ["get", "watch", "list", "create", "delete"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "watch", "list", "create", "delete"]

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: michelle-storage-admin
subjects:
- kind: User
  name: michelle
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: storage-admin
  apiGroup: rbac.authorization.k8s.io
```