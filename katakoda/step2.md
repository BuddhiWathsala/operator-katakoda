Install a Siddhi application which have HTTP source and log sink. This app consumes events from HTTP as a JSON message of { 'deviceType': 'dryer', 'power': 6000 } format and inserts the events into DevicePowerStream, and alerts the user if the power level is greater than or equal to 600W by printing a message in the log.

## Task 1

Create a persistence volume to persist the state of the Siddhi app.

`kubectl apply -f deploy/examples/example-pv.yaml`{{execute}}


## Task 2

Deploy the stateful app.

`kubectl apply -f deploy/examples/example-stateful-log-app.yaml`{{execute}}


## Task 3

Add Siddhi host to the /etc/hosts file along with the minikube IP.

``` echo " `minikube ip` siddhi" >> /etc/hosts ```{{execute}}

Wait until all pods come to the running state.

`kubectl get pods`{{execute}}


## Task 4

Send an HTTP event.

```
    curl -X POST \
    http://siddhi/power-surge-app-0/8080/checkPower \
    -H 'Accept: */*' \
    -H 'Content-Type: application/json' \
    -H 'Host: siddhi' \
    -d '{
    "deviceType": "dryer",
    "power": 60000
    }'
```{{execute}}


## Task 5

View logs.

`kubectl logs $(kubectl get pods | awk '{ print $1 }' | grep ^power-consume-app-1)`{{execute}}
