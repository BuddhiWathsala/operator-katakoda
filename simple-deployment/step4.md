Application can be tested by sending HTTP requests. We can send multiple HTTP requests using the curl command as given below.

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

When Siddhi app receives a total of more than 10000W in last 1 minute it will print a log once every 30 seconds.Use the following command to view logs. Since the logs will print in every 30 seconds, at times we might need to wait for a couple of seconds to view the logs.

`kubectl logs $(kubectl get pods | awk '{ print $1 }' | grep ^power-consume-app-1) | tail -n 10`{{execute}}

The expected output logs will be as following.

## TODO 
