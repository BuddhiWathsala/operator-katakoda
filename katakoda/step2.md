Install a Siddhi application which have HTTP source and log sink. This app consumes events from HTTP as a JSON message of { 'deviceType': 'dryer', 'power': 6000 } format and inserts the events into DevicePowerStream, and alerts the user if the power level is greater than or equal to 600W by printing a message in the log.

## Task 1

`kubectl apply -f deploy/examples/example-stateless-log-app.yaml`{{execute}}

``` echo " `minikube ip` siddhi" >> /etc/hosts ```{{execute}}

`curl -X POST http://siddhi/power-surge-app-0/8080/checkPower -H 'Accept: */*' -H 'Content-Type: application/json' -H 'Host: siddhi' -d '{"deviceType": "dryer", "power": 60000}'`{{execute}}
