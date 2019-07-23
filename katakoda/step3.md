Install a Siddhi application which has HTTP source and log sink. This app consumes events from HTTP as a JSON message of { 'deviceType': 'dryer', 'power': 6000 } format and inserts the events into DevicePowerStream, and alerts the user if the power level is greater than or equal to 600W by printing a message in the log.

## Task 1

Please wait until all pods come to the running state. To check all pods are in running state use the following command.

`kubectl get pods`{{execute}}


## Task 2

View the stateful app.

`cat deploy/examples/example-stateful-log-app.yaml`{{execute}}


## Task 3

Deploy the stateful app.

`kubectl apply -f deploy/examples/example-stateful-log-app.yaml`{{execute}}


## Task 4

Add Siddhi host to the /etc/hosts file along with the minikube IP.

``` echo " `minikube ip` siddhi" >> /etc/hosts ```{{execute}}

Please wait until all pods come to the running state. To check all pods are in running state use the following command.

`kubectl get pods`{{execute}}


## Task 5

Send an event using an HTTP request. You can send multiple HTTP requests. The Siddhi app will print the log in every 30 seconds if the total power you send is greater than or equal to 10000W.

```
    curl -X POST \
    http://siddhi/power-consume-app-0/8080/checkPower \
    -H 'Accept: */*' \
    -H 'Content-Type: application/json' \
    -H 'Host: siddhi' \
    -d '{
    "deviceType": "dryer",
    "power": 100000
    }'
```{{execute}}


## Task 6

Use the following command to view logs. Logs will print in every 30 seconds.

`kubectl logs $(kubectl get pods | awk '{ print $1 }' | grep ^power-consume-app-1) | tail -n 2`{{execute}}
