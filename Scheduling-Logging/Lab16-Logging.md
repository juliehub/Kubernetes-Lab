1. A user - 'USER5' - has expressed concerns accessing the application. Identify the cause of the issue.
Inspect the logs of the POD
```bash
master $ kubectl get pods
NAME       READY   STATUS    RESTARTS   AGE
webapp-1   1/1     Running   0          84s
master $ kubectl logs webapp-1 | grep USER5
[2020-09-18 09:59:39,991] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-09-18 09:59:44,998] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-09-18 09:59:50,006] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-09-18 09:59:55,012] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
```
2. A user is reporting issues while trying to purchase an item. Identify the user and the cause of the issue.
Inspect the logs of the webapp in the POD
```bash
master $ kubectl logs webapp-2 -c simple-webapp |grep WARNING
[2020-09-18 10:04:20,802] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-09-18 10:04:23,806] WARNING in event-simulator: USER30 Order failed as the item is OUT OF STOCK.
[2020-09-18 10:04:25,808] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
```
