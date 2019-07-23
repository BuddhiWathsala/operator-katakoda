Now it is time to install the Siddhi operator. Siddhi operator installation uses two YAML files.

1. Prerequisite file which contained all the configurations need by the operator like CRD, service accounts, roles, and role bindings.
2. Operator deployment file that contained operator deployment and the parser deployment.

`git clone https://github.com/BuddhiWathsala/siddhi-operator.git`{{execute}}

`cd siddhi-operator`{{execute}}

`git checkout buddhi-versioning`{{execute}}

`kubectl create -f ./deploy/siddhi_v1alpha2_siddhiprocess_crd.yaml`{{execute}}

`kubectl create -f ./deploy/service_account.yaml`{{execute}}

`kubectl create -f ./deploy/role.yaml`{{execute}}

`kubectl create -f ./deploy/role_binding.yaml`{{execute}}

`kubectl create -f ./deploy/operator.yaml`{{execute}}

