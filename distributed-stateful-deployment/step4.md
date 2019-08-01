Siddhi operator divides the given Siddhi app into two partial Siddhi apps and deploys both apps in two kubernetes deployments. Those two apps are,

1. Passthrough app (power-consume-app-0)
1. Process app  (power-consume-app-1)

Passthrough app receives HTTP requests and redirects those requests to the NATS streaming cluster. Process app receives events from NATS, execute the logic, and logs the output.

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

Send an event using an HTTP request. You can send multiple HTTP requests. The Siddhi app will print the log in every 30 seconds if the total power you send is greater than 10000W.

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

Use the following command to view logs. Logs will print in every 30 seconds, therefore please wait for a couple of seconds to view the logs.

`kubectl logs $(kubectl get pods | awk '{ print $1 }' | grep ^power-consume-app-1) | tail -n 10`{{execute}}

The expected output logs will be as following.

```
$ kubectl logs $(kubectl get pods | awk '{ print $1 }' | grep ^power-consume-app-1) | tail -n 10

[2019-07-31 10:19:22,671]  INFO {org.wso2.carbon.databridge.receiver.binary.internal.BinaryDataReceiver} - Started Binary TCP Transport on port : 9612
[2019-07-31 10:19:22,673]  INFO {org.wso2.carbon.databridge.receiver.thrift.internal.ThriftDataReceiverDS} - Service Component is activated
[2019-07-31 10:19:22,684]  INFO {org.wso2.carbon.databridge.receiver.thrift.ThriftDataReceiver} - Thrift Server started at 0.0.0.0
[2019-07-31 10:19:22,692]  INFO {org.wso2.carbon.databridge.receiver.thrift.ThriftDataReceiver} - Thrift SSL port : 7711
[2019-07-31 10:19:22,694]  INFO {org.wso2.carbon.databridge.receiver.thrift.ThriftDataReceiver} - Thrift port : 7611
[2019-07-31 10:19:22,791]  INFO {org.wso2.msf4j.internal.MicroservicesServerSC} - All microservices are available
[2019-07-31 10:19:22,838]  INFO {org.wso2.transport.http.netty.contractimpl.listener.ServerConnectorBootstrap$HttpServerConnector} - HTTP(S) Interface starting on host 0.0.0.0 and port 9090
[2019-07-31 10:19:22,840]  INFO {org.wso2.transport.http.netty.contractimpl.listener.ServerConnectorBootstrap$HttpServerConnector} - HTTP(S) Interface starting on host 0.0.0.0 and port 9443
[2019-07-31 10:19:22,844]  INFO {org.wso2.carbon.kernel.internal.CarbonStartupHandler} - Siddhi Runner Distribution started in 3.005 sec
[2019-07-31 10:25:52,485]  INFO {io.siddhi.core.stream.output.sink.LogSink} - LOGGER : Event{timestamp=1564568739939, data=[dryer, 100000], isExpired=false}
```