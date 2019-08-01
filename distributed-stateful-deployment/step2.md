## Enabling NGINX ingress

As Siddhi operator by default uses NGINX ingress controller to receive HTTP/HTTPS requests. Hence [enable ingress](https://kubernetes.github.io/ingress-nginx/deploy/) in your minikube kubernetes cluster using the following command.

`minikube addons enable ingress`{{execute}}

Minikube uses the minikube IP as the ingress external IP, and since Siddhi operator uses hostname called `siddhi` to receive all external traffic we need to add the siddhi entry to the `/etc/hosts` file using the below command.

``` echo " `minikube ip` siddhi" >> /etc/hosts ```{{execute}}

## Deploy NATS and NATS Streaming

Siddhi operator splits the given Siddhi App into partial apps and connects using NATS and NATS Streaming systems to communicate between the distributed applications. Use the following commands to install the NATS and NATS streaming systems.

`kubectl apply -f https://github.com/nats-io/nats-operator/releases/download/v0.5.0/00-prereqs.yaml`{{execute}}

`kubectl apply -f https://github.com/nats-io/nats-operator/releases/download/v0.5.0/10-deployment.yaml`{{execute}}

`kubectl apply -f https://github.com/nats-io/nats-streaming-operator/releases/download/v0.2.2/default-rbac.yaml`{{execute}}

`kubectl apply -f https://github.com/nats-io/nats-streaming-operator/releases/download/v0.2.2/deployment.yaml`{{execute}}

## Setup Persistence Volume

The stateful Siddhi app deployment needs a Kubernetes persistence volume to preserve the state of the Siddhi app. To do that in minikube first you have to download this YAML file that contains the K8s persistence volume specification.

`wget https://raw.githubusercontent.com/siddhi-io/siddhi-operator/master/deploy/examples/example-pv.yaml`{{execute}}

You can see the persistence volume YAML using the following command.

`cat example-pv.yaml`{{execute}}

Now you can deploy the persistence volume using the following command.

`kubectl apply -f example-pv.yaml`{{execute}}

Siddhi runner docker image runs by a user called `siddhi_user`. The `siddhi_user` belongs to the `siddhi_io` user group. Here we mount `/home/siddhi_user/` directory as the persistence volume. Hence we need to change the ownership of that director to the `siddhi_user` in the `siddhi_io` user group.

You have to create the `siddhi_user` and the `siddhi_group` using the following commands.

`sudo /usr/sbin/addgroup --system -gid 802 siddhi_io`{{execute}}

`sudo /usr/sbin/adduser --system -gid 802 -uid 802 siddhi_user`{{execute}}

After that change the ownership of the directory using the following command.

`sudo chown siddhi_user:siddhi_io /home/siddhi_user/`{{execute}}


## Install Siddhi operator

Deploy the necessary prerequisite such as  CRD, service accounts, roles, and role bindings using the following command.

`kubectl apply -f https://github.com/BuddhiWathsala/siddhi-operator/releases/download/0.2.0-m2/00-prereqs.yaml`{{execute}}

`kubectl apply -f https://github.com/BuddhiWathsala/siddhi-operator/releases/download/0.2.0-m2/01-siddhi-operator.yaml`{{execute}}

## Validate the Environment

Ensure that all necessary pods in the cluster up and running using the following command.

`kubectl get pods`{{execute}}

Make sure the all the following 4 pods are up and running.

```sh
$ kubectl get pods
NAME                                       READY     STATUS    RESTARTS   AGE
nats-operator-dd7f4945f-x4vf8              1/1       Running   0          10m
nats-streaming-operator-6fbb6695ff-9rmlx   1/1       Running   0          10m
siddhi-operator-6698d8f69d-w2kvj           1/1       Running   0          10m
siddhi-parser-76448887d5-hgnrw             1/1       Running   0          10m
```

The next section provides information on Deploying Stateful Siddhi App.

