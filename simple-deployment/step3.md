Here we will deploy a stateless Siddhi app that we have discussed in the introduction section.

A SiddhiProcess YAML to deploy the application can be retrieved as bellow.

`wget https://raw.githubusercontent.com/siddhi-io/siddhi-operator/master/deploy/examples/example-stateless-log-app.yaml`{{execute}}

View the SiddhiProcess YAML as following.

`cat example-stateless-log-app.yaml`{{execute}}

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
define stream PowerSurgeAlertStream(deviceType string, power int);
```

The execution logic of the Siddhi app defined by the following query.

```programming
@info(name='surge-detector')  
from DevicePowerStream[deviceType == 'dryer' and power >= 600] 
select deviceType, power  
insert into PowerSurgeAlertStream;
```

Above query executes the following tasks.
1. Receive HTTP events.
1. Filter out all the HTTP events which have the device type as dryer and the power consumption greater than or equals to 600W.
1. Output the filtered events.

Now you can deploy the stateless Siddhi App.

`kubectl apply -f example-stateless-log-app.yaml`{{execute}}

Validate the app is deployed correctly by running.

`kubectl get pods`{{execute}}

```sh
$ kubectl get pods

NAME                                 READY     STATUS    RESTARTS   AGE
power-surge-app-0-57f687cc85-8k9d6   1/1       Running   0          2m
siddhi-operator-6f7d8f7556-j9j89     1/1       Running   0          5m
siddhi-parser-547cdd7885-5rcz9       1/1       Running   0          5m
```

You can view the `SiddhiProcess` using the following commands.

```sh
$ kubectl get sp

NAME              STATUS    READY     AGE
power-surge-app   Running   1/1       5m
```
