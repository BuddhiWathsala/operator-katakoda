Siddhi is a cloud-native Streaming and Complex Event Processing engine that understands Streaming SQL queries to capture events from diverse data sources, process them, detect complex conditions, and publish output to various endpoints in real-time. 

Siddhi operator allows you to run Siddhi stream processing logic directly on a Kubernetes cluster.

Siddhi uses various types of sources to receive events like HTTP, Kafka, NATS, and Prometheus, etc. Siddhi operator by default uses NGINX ingress controller to receive HTTP/HTTPS requests. Therefore first you have to [enable ingress](https://kubernetes.github.io/ingress-nginx/deploy/) in your kubernetes cluster. Use the following command to enable NGINX ingress controller in this minikube cluster.

`minikube addons enable ingress`{{execute}}

Siddhi supports two different deployment types.

1. Default deployment
1. Distributed deployment

In this example, we are planning to deploy a basic distributed Siddhi app using the Siddhi operator. All the partial apps that deploying in this distributed deployment connected to a messaging system. The distributed deployment use this messaging system to receive messages without any dropouts. Siddhi operator supports NATS as the default messaging system. Now we need to install the NATS operator into the Kubernetes cluster.

`kubectl apply -f https://github.com/nats-io/nats-operator/releases/download/v0.5.0/00-prereqs.yaml`{{execute}}

`kubectl apply -f https://github.com/nats-io/nats-operator/releases/download/v0.5.0/10-deployment.yaml`{{execute}}


NATS server more concern about the simplicity, performance, and ease of use. For extended functionalities, we need NATS streaming server. Also, the [NATS extension](https://github.com/siddhi-io/siddhi-io-nats) for Siddhi connect to the NATS streaming server. The NATS streaming operator is also a prerequisite for the Siddhi distributed deployment.

`kubectl apply -f https://raw.githubusercontent.com/nats-io/nats-streaming-operator/master/deploy/default-rbac.yaml`{{execute}}

`kubectl apply -f https://raw.githubusercontent.com/nats-io/nats-streaming-operator/master/deploy/deployment.yaml`{{execute}}
