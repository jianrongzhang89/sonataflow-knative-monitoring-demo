# sonataflow-knative-monitoring-demo
A quick demo for SonataFlow, Knative and Monitoring integration

## Deploy PostgreSQL Server 
```
oc new-project sonataflow-infra
oc create secret generic sonataflow-psql-postgresql --from-literal=postgres-username=postgres --from-literal=postgres-password=postgres
git clone git@github.com:rhdhorchestrator/orchestrator-helm-chart.git
cd orchestrator-helm-chart/postgresql
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install sonataflow-psql bitnami/postgresql --version 12.x.x -f ./values.yaml
```
Note that the names of the secret and keys must match the ones used in the SonataFlowPlatform and workflow definition.

## Deploy Orchestrator Operator from OpenShift OperatorHub
## Create Orchestrator CR in sonataflow-infra namespace:
    a) Make sure monitoring is enabled in the SonataFlowPlatform.
    b) Set SonataFlowPlatform builder memory limit to 2Gi.
    b) Set eventing broker to default.
    c) Disable RHDH Operator.
## Create the default broker
After the OpenShift Serverless Operator is up and running, create the default broker.
```
    oc apply -f broker.yaml
```
## Check dataindex and job service pods
Check and confirm that the dataindex and job service pods are running.

## Deploy and configure Promtheus and Grafana
Follow the instructions at https://sonataflow.org/serverlessworkflow/latest/cloud/operator/monitoring-workflows.html to deploy and configure Promtheus and Grafana.

In order to access the Prometheus UI, first run the command
```
oc port-forward -n openshift-user-workload-monitoring pod/prometheus-user-workload-0 9090
```
and then use URL `http://localhost:9090/graph` in a web browser to open the Prometheus UI. 

## Verify the Grafana DataSource connection
## Import the sample Grafana dashboard
## Deploy the sample workflows
```
    oc apply -f sonataflow_eventstatetimeouts.sw.yaml
    oc apply -f sonataflow_greeting.yaml
    oc apply -f sonataflow_callbackstatetimeouts.sw.yaml
```
## Trigger workflow instances
Run the curl commands below to trigger workflow instances.
```
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' http://callbackstatetimeouts/callbackstatetimeouts
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' http://eventstatetimeouts/eventstatetimeouts
// For Greeting
curl -X POST -H 'Content-Type:application/json' -H 'Accept:application/json' -d '{"name": "John", "language": "English"}'    http://greeting/greeting
curl -X POST -H 'Content-Type:application/json' -H 'Accept:application/json' -d '{"name": "John", "language": "Spanish"}'    http://greeting/greeting
```
## Grafana Dashboard
Verify that the dashboard shows the workflow instances appropriately.

