Here we will  deploy a stateful Siddhi app that we have discussed in the introduction section.

A SiddhiProcess YAML to deploy the application can be retrieved as bellow.

`wget https://raw.githubusercontent.com/siddhi-io/siddhi-operator/master/deploy/examples/example-stateful-log-app.yaml`{{execute}}

View the SiddhiProcess yaml as following.

`cat example-stateful-log-app.yaml`{{execute}}

This Siddhi application uses an HTTP source like below to receive events.

```programming
@source(
    type='http',
    receiver.url='${RECEIVER_URL}',
    basic.auth.enabled='false',
    @map(type='json')
)
define stream DevicePowerStream(deviceType string, power int);
```

And print events using the log sink.

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

Above query executes the following tasks.
1. Retains events arrived in last  1 minute period
1. Group all the events by the electronic device type and calculate the total power consumption
1. Select all the devices which exceed 1000W power consumption
1. Output aggregated events once in each 30 seconds

By specifying only the messaging system name as NATS like below, Siddhi operator will automatically create the NATS cluster and the NATS streaming cluster to wire the distributed Siddhi Apps.

```yaml
messagingSystem:
    type: nats
```

Now you can deploy the Stateful Siddhi App.

`kubectl apply -f example-stateful-log-app.yaml`{{execute}}

Validate the app is deployed correctly by running.

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

Here the Siddhi operator divides the given Siddhi app into two partial Siddhi apps and deploys both apps in two kubernetes deployments as follows,
1. Passthrough app (power-consume-app-0)
1. Process app (power-consume-app-1)

Where passthrough app receives HTTP requests and redirects those requests to the NATS streaming cluster to the Process app to execute the logic in a stateful way and produce the output via logs. Here the `siddhi-nats-1` pod is the NATS cluster which automatically created by the Siddhi operator. The `siddhi-stan-1 ` pod is the NATS streaming cluster which automatically created by the Siddhi operator. NATS is the default naming convension for name NATS clusters and STAN is the default naming convension for name streaming cluster.

You can view the `SiddhiProcess` using the following commands.

```sh
$ kubectl get sp

NAME                STATUS    READY     AGE
power-consume-app   Running   2/2       5m
```

