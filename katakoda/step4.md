Siddhi operator divide the given Siddhi app into two partial Siddhi apps and deploy both apps in two kubernetes deployments. Those two apps are,

1. Passthrough app - power-consume-app-0
1. Process app - power-consume-app-1

Passthrough app recieves HTTP requests and redirect those requests to NATS streaming cluster. Process app receives events from NATS, execute the logic, and logs the output.

Since Passthrough app contains an HTTP source, Siddhi operator by default creates a kubernetes service for the deployment and expose the HTTP endpoint through an ingress. Minikube embbed the minikube IP to the ingress. Siddhi operator uses hostname called `siddhi` to receive all external traffic. Thereofore we need to add the `siddhi` entry to the `/etc/hosts` file like below.

``` echo " `minikube ip` siddhi" >> /etc/hosts ```{{execute}}

Before sending any request we need to ensure that all pods are up and running.

`kubectl get pods`{{execute}}

```sh
$ kubectl get pods
NAME                                       READY     STATUS    RESTARTS   AGE
nats-operator-dd7f4945f-x4vf8              1/1       Running   0          10m
nats-streaming-operator-6fbb6695ff-9rmlx   1/1       Running   0          10m
power-consume-app-0-7486b87979-6tccx       1/1       Running   0          5m
power-consume-app-1-588996fcfb-prncj       1/1       Running   0          5m
siddhi-nats-1                              1/1       Running   0          5m
siddhi-operator-6698d8f69d-w2kvj           1/1       Running   0          10m
siddhi-parser-76448887d5-hgnrw             1/1       Running   0          10m
siddhi-stan-1                              1/1       Running   1          5m
```

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

Use the following command to view logs. Logs will print in every 30 seconds.

`kubectl logs $(kubectl get pods | awk '{ print $1 }' | grep ^power-consume-app-1) | tail -n 2`{{execute}}
