1. Where is the default kubeconfig file located in the current environment? /root/.kube/config
Find the current home directory by looking at the HOME environment variable
```bash
master $ cd /root/.kube
master $ ls
cache  config  http-cache
```
2. How many clusters are defined in the default kubeconfig file? 1
```bash
master $ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://172.17.0.25:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
```
3. How many users are defined in the default kubeconfig file? 1

4. How many contexts are defined in the default kubeconfig file? 1

5. What is the user configured in the current context? kubernetes-admin

6. What is the name of the cluster configured in the default kubeconfig file? kubernetes

7. A new kubeconfig file named 'my-kube-config' is created. It is placed in the /root directory. How many clusters are defined in the kubectl file: 4
```bash
master $ kubectl config view --kubeconfig my-kube-config
apiVersion: v1
clusters: null
contexts: null
current-context: ""
kind: Config
preferences: {}
users: null
master $ cat my-kube-config
apiVersion: v1
kind: Config

clusters:
- name: production
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://172.17.0.25:6443

- name: development
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://172.17.0.25:6443

- name: kubernetes-on-aws
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://172.17.0.25:6443

- name: test-cluster-1
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://172.17.0.25:6443

contexts:
- name: test-user@development
  context:
    cluster: development
    user: test-user

- name: aws-user@kubernetes-on-aws
  context:
    cluster: kubernetes-on-aws
    user: aws-user

- name: test-user@production
  context:
    cluster: production
    user: test-user

- name: research
  context:
    cluster: test-cluster-1
    user: dev-user

users:
- name: test-user
  user:
    client-certificate: /etc/kubernetes/pki/users/test-user/test-user.crt
    client-key: /etc/kubernetes/pki/users/test-user/test-user.key
- name: dev-user
  user:
    client-certificate: /etc/kubernetes/pki/users/dev-user/developer-user.crt
    client-key: /etc/kubernetes/pki/users/dev-user/dev-user.key
- name: aws-user
  user:
    client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
    client-key: /etc/kubernetes/pki/users/aws-user/aws-user.key

current-context: test-user@development
preferences: {}
```
8. How many contexts are configured in the 'my-kube-config' file? 4

9. What user is configured in the 'research' context? dev-user

10. What is the name of the client-certificate file configured for the 'aws-user'?
/etc/kubernetes/pki/users/aws-user/aws-user.crt

11. What is the current context set to in the 'my-kube-config' file? test-user@development

12. I would like to use the dev-user to access test-cluster-1. Set the current context to the right one so I can do that.
Once the right context is identified, use the 'kubectl config use-context' command.
```bash
master $ kubectl config --kubeconfig=/root/my-kube-config use-context research
Switched to context "research".
```

13. We don't want to have to specify the kubeconfig file option on each command. Make the my-kube-config file the default kubeconfig.
```bash
master $ cp my-kube-config .kube/config
```
