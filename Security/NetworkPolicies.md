1. How many network policies do you see in the environment? `1`
We have deployed few web applications, services and network policies. Inspect the environment.
```bash
master $ kubectl get networkpolicy
NAME             POD-SELECTOR   AGE
payroll-policy   name=payroll   4m17s
```
2. What is the name of the Network Policy? `payroll-policy`

3. Which pod is the Network Policy applied on? `payroll`

4. What type of traffic is this Network Policy configured to handle? `Ingress`
```bash
master $ kubectl describe networkpolicy payroll-policy
Name:         payroll-policy
Namespace:    default
Created on:   2020-10-07 04:46:41 +0000 UTC
Labels:       <none>
Annotations:  Spec:
  PodSelector:     name=payroll
  Allowing ingress traffic:
    To Port: 8080/TCP
    From:
      PodSelector: name=internal
  Not affecting egress traffic
  Policy Types: Ingress
```
5. What is the impact of the rule configured on this Network Policy?
Answer: Traffic from Internal to Payroll POD is allowed

6. What is the impact of the rule configured on this Network Policy?

Answer: Internal pod can access port 8080 on payroll pod

7. Perform a connectivity test using the User Interface in these Applications to access the 'payroll-service' at port '8080'.

Answer: Only internal applications can access payroll service

8. Perform a connectivity test using the User Interface of the Internal Application to access the 'external-service' at port '8080'.

Answer: Successful

9. Create a network policy to allow traffic from the 'Internal' application only to the 'payroll-service' and 'db-service'
Use the spec given on the right. You might want to enable ingress traffic to the pod to test your rules in the UI.
- Policy Name: internal-policy
- Policy Types: Egress
- Egress Allow: payroll
- Payroll Port: 8080
- Egress Allow: mysql
- MYSQL Port: 3306
```bash
master $ cat internal-policy.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
  - Egress
  - Ingress
  ingress:
    - {}
  egress:
  - to:
    - podSelector:
        matchLabels:
          name: mysql
    ports:
    - protocol: TCP
      port: 3306

  - to:
    - podSelector:
        matchLabels:
          name: payroll
    ports:
    - protocol: TCP
      port: 8080

  - ports:
    - port: 53
      protocol: UDP
    - port: 53
      protocol: TCP
```
```bash
master $ kubectl create -f internal-policy.yaml
networkpolicy.networking.k8s.io/internal-policy created
```
```bash
master $ kubectl get networkpolicy
NAME              POD-SELECTOR    AGE
internal-policy   name=internal   21s
payroll-policy    name=payroll    35m
master $ kubectl describe networkpolicy internal-policy
Name:         internal-policy
Namespace:    default
Created on:   2020-10-07 05:21:46 +0000 UTC
Labels:       <none>
Annotations:  <none>
Spec:
  PodSelector:     name=internal
  Allowing ingress traffic:
    To Port: <any> (traffic allowed to all ports)
    From: <any> (traffic not restricted by source)
  Allowing egress traffic:
    To Port: 3306/TCP
    To:
      PodSelector: name=mysql
    ----------
    To Port: 8080/TCP
    To:
      PodSelector: name=payroll
    ----------
    To Port: 53/UDP
    To Port: 53/TCP
    To: <any> (traffic not restricted by source)
  Policy Types: Egress, Ingress
```






