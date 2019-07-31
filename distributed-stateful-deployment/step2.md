## Enabling NGINX ingress

As Siddhi operator by default uses NGINX ingress controller to receive HTTP/HTTPS requests. Hence [enable ingress](https://kubernetes.github.io/ingress-nginx/deploy/) in your minikube kubernetes cluster using the following command.

`minikube addons enable ingress`{{execute}}

Minikube uses the minikube IP as the ingress external IP, and since Siddhi operator uses hostname called siddhi to receive all external traffic we need to add the siddhi entry to the /etc/hosts file using the below command.

``` echo " `minikube ip` siddhi" >> /etc/hosts ```{{execute}}

## Deploy NATS and NATS Streaming

Siddhi operator splits the given Siddhi App into partial apps and connect with each other using NATS and NATS Streaming systems to communicate between the distributed applications. Use the following commads to install the NATS and NATS streaming systems.


`kubectl apply -f https://github.com/nats-io/nats-operator/releases/download/v0.5.0/00-prereqs.yaml`{{execute}}

`kubectl apply -f https://github.com/nats-io/nats-operator/releases/download/v0.5.0/10-deployment.yaml`{{execute}}

`kubectl apply -f https://github.com/nats-io/nats-streaming-operator/releases/download/v0.2.2/default-rbac.yaml`{{execute}}

`kubectl apply -f https://github.com/nats-io/nats-streaming-operator/releases/download/v0.2.2/deployment.yaml`{{execute}}

## Setup Persistence Volume

## Install Siddhi operator

Deploy the necessary prerequisite such as  CRD, service accounts, roles, and role bindings using the following command.

`kubectl apply -f https://github.com/BuddhiWathsala/siddhi-operator/releases/download/0.2.0-m2/00-prereqs.yaml`{{execute}}

`kubectl apply -f https://github.com/BuddhiWathsala/siddhi-operator/releases/download/0.2.0-m2/01-siddhi-operator.yaml`{{execute}}

## Validate the Environment

Ensure that all necessary pods in the cluster up and running using the following command.

`kubectl get pods`{{execute}}

Make sure the all the following 4 pods are up and running,

```sh
$ kubectl get pods
NAME                                       READY     STATUS    RESTARTS   AGE
nats-operator-dd7f4945f-x4vf8              1/1       Running   0          10m
nats-streaming-operator-6fbb6695ff-9rmlx   1/1       Running   0          10m
siddhi-operator-6698d8f69d-w2kvj           1/1       Running   0          10m
siddhi-parser-76448887d5-hgnrw             1/1       Running   0          10m
```

The next section provides information on Deploying Stateful Siddhi App.

