Here we will deploy a stateful Siddhi app that we have discussed in the introduction section.

A SiddhiProcess YAML to deploy the application can be retrieved as bellow.

`wget https://raw.githubusercontent.com/siddhi-io/siddhi-operator/master/deploy/examples/example-stateful-monolithic-log-app.yaml`{{execute}}

View the SiddhiProcess YAML as following.

`cat example-stateful-monolithic-log-app.yaml`{{execute}}

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

Now you can deploy the Stateful Siddhi App.

`kubectl apply -f example-stateful-monolithic-log-app.yaml`{{execute}}

Validate the app is deployed correctly by running.

`kubectl get pods`{{execute}}

```sh
$ kubectl get pods
NAME                                  READY     STATUS    RESTARTS   AGE
power-consume-app-0-886559b77-6w6lh   1/1       Running   0          2m
siddhi-operator-6f7d8f7556-j9j89      1/1       Running   0          5m
siddhi-parser-7847c7dd67-kf4xk        1/1       Running   0          5m
```

You can view the `SiddhiProcess` using the following commands.

```sh
$ kubectl get sp

NAME                STATUS    READY     AGE
power-consume-app   Running   2/2       2m
```

