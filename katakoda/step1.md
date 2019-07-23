Here we are going to deploy a stateful Siddhi app using Siddhi process CRD. You can follow these steps and deploy that Siddhi app very easily using a couple of kubectl commands.

## Task 1

Enable NGINX ingress controller.

`minikube addons enable ingress`{{execute}}


## Task 2

Install NATS Operator.

`kubectl apply -f https://github.com/nats-io/nats-operator/releases/download/v0.5.0/00-prereqs.yaml`{{execute}}

`kubectl apply -f https://github.com/nats-io/nats-operator/releases/download/v0.5.0/10-deployment.yaml`{{execute}}


## Task 3

Install NATS Streaming Operator.

`kubectl apply -f https://raw.githubusercontent.com/nats-io/nats-streaming-operator/master/deploy/default-rbac.yaml`{{execute}}

`kubectl apply -f https://raw.githubusercontent.com/nats-io/nats-streaming-operator/master/deploy/deployment.yaml`{{execute}}


## Task 4

Install Siddhi Operator.

`git clone https://github.com/BuddhiWathsala/siddhi-operator.git`{{execute}}

`cd siddhi-operator`{{execute}}

`git checkout buddhi-versioning`{{execute}}

`kubectl create -f ./deploy/siddhi_v1alpha2_siddhiprocess_crd.yaml`{{execute}}

`kubectl create -f ./deploy/service_account.yaml`{{execute}}

`kubectl create -f ./deploy/role.yaml`{{execute}}

`kubectl create -f ./deploy/role_binding.yaml`{{execute}}

`kubectl create -f ./deploy/operator.yaml`{{execute}}

