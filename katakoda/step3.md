Now we are going to deploy a stateful Siddhi app. This Siddhi application listning to a event stream that provides details of power consumption in each electric item in a house. If the power consumption of the dryer exceed 10000W within a 1 minute time period then the Siddhi application will sends an log event to the user. This log event will print for every 30 seconds.

The Siddhi application uses a HTTP source like below to receive events.

```programming
@source(
    type='http',
    receiver.url='${RECEIVER_URL}',
    basic.auth.enabled='false',
    @map(type='json')
)
define stream DevicePowerStream(deviceType string, power int);
```

And logs events using the log sink.

```programming
@sink(type='log', prefix='LOGGER') 
define stream PowerSurgeAlertStream(deviceType string, powerConsumed long);
```

The execution logic of the Siddhi app defined by the following query.

```programming
@info(name='power-consumption-window')  
from DevicePowerStream#window.time(1 min) 
select deviceType, sum(power) as powerConsumed
group by deviceType
having powerConsumed > 10000
output every 30 sec
insert into PowerSurgeAlertStream;
```

This query that executes in the Siddhi app do the following tasks.
1. Receive events from the HTTP source in 1 minute period
1. Group all the events by the electronic device type
1. Select all the devices which excedd 1000W power consumption
1. Output aggregated events in each 30 seconds

Note that if you just specify only the messaging system name as NATS like below, Siddhi operator will automatically creates the NATS cluster and the NATS streaming cluster.

```yaml
messagingSystem:
    type: nats
```

Before install the Siddhi apps you have to ensure that all pods in the cluster up and running like below.

`kubectl get pods`{{execute}}

```sh
$ kubectl get pods
NAME                                       READY     STATUS    RESTARTS   AGE
nats-operator-dd7f4945f-x4vf8              1/1       Running   0          10m
nats-streaming-operator-6fbb6695ff-9rmlx   1/1       Running   0          10m
siddhi-operator-6698d8f69d-w2kvj           1/1       Running   0          10m
siddhi-parser-76448887d5-hgnrw             1/1       Running   0          10m
```

Now you can clone the Siddhi operator GitHub repository and install the sample.

`git clone https://github.com/BuddhiWathsala/siddhi-operator.git`{{execute}}

`cd siddhi-operator`{{execute}}

`kubectl apply -f deploy/examples/example-stateful-log-app.yaml`{{execute}}
