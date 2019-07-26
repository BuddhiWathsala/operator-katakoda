Now it is time to install the Siddhi operator. Siddhi operator installation uses two YAML files.

1. Prerequisite file which contains all the configurations needed by the operator like CRD, service accounts, roles, and role bindings.
2. Operator deployment file that contained operator deployment and the parser deployment.

`kubectl apply -f https://github.com/BuddhiWathsala/siddhi-operator/releases/download/0.2.0-m2/00-prereqs.yaml`{{execute}}

`kubectl apply -f https://github.com/BuddhiWathsala/siddhi-operator/releases/download/0.2.0-m2/01-siddhi-operator.yaml`{{execute}}
