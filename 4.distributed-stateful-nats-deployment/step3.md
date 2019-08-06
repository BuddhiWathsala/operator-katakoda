Here we will deploy a stateful Siddhi app that we have discussed in the introduction section.

A SiddhiProcess YAML to deploy the application can be retrieved as bellow.

`wget https://raw.githubusercontent.com/siddhi-io/siddhi-operator/master/deploy/examples/example-stateful-distributed-nats-app.yaml`{{execute}}

View the SiddhiProcess YAML as following.

`cat example-stateful-distributed-nats-app.yaml`{{execute}}

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
@info(name='surge-detector')
from DevicePowerStream#window.time(1 min)
select deviceType, sum(power) as powerConsumed
group by deviceType
having powerConsumed > 10000
output every 30 sec
insert into PowerSurgeAlertStream;
```

Above query executes the following tasks.
1. Retains events arrived in last 1 minute period
1. Group all the events by the electronic device type and calculate the total power consumption
1. Select all the devices which exceed 1000W power consumption
1. Output aggregated events once in each 30 seconds

The YAML file specifying the configurations of the messaging system that you created previously like below. From that, Siddhi operator parses NATS configurations to the partial Siddhi apps. Those partial Siddhi apps will individually connect to the NATS from those configurations.

```yaml
messagingSystem:
  type: nats
  config: 
    bootstrapServers: 
      - "nats://nats-siddhi:4222"
    clusterID: stan-siddhi
```

Now you can deploy the Stateful Siddhi App.

`kubectl apply -f example-stateful-distributed-nats-app.yaml`{{execute}}

Validate the app is deployed correctly by running.

`kubectl get pods`{{execute}}

```sh
$ kubectl get pods
NAME                                       READY     STATUS    RESTARTS   AGE
nats-operator-b8f4977fc-jdknv              1/1       Running   0          5m
nats-siddhi-1                              1/1       Running   0          5m
nats-streaming-operator-64b565bcc7-r95fl   1/1       Running   0          5m
power-consume-app-0-886559b77-hq2jk        1/1       Running   0          2m
power-consume-app-1-648b76d86d-6cxnx       1/1       Running   0          2m
siddhi-operator-6f7d8f7556-j9j89           1/1       Running   0          5m
stan-siddhi-1                              1/1       Running   0          5m
```

Here the Siddhi operator divides the given Siddhi app into two partial Siddhi apps and deploys both apps in two kubernetes deployments as follows,
1. Passthrough app (power-consume-app-0)
1. Process app (power-consume-app-1)

Where passthrough app receives HTTP requests and redirects those requests to the NATS streaming cluster to the Process app to execute the logic in a stateful way and produce the output via logs. Here the `siddhi-nats-1` pod is the NATS cluster which automatically created by the Siddhi operator. The `siddhi-stan-1` pod is the NATS streaming cluster which automatically created by the Siddhi operator. NATS is the default naming convention for name NATS clusters and STAN is the default naming convention for name streaming cluster.

You can view the `SiddhiProcess` using the following commands.

```sh
$ kubectl get sp

NAME                STATUS    READY     AGE
power-consume-app   Running   2/2       5m
```

