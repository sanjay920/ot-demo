# Shipping traces to Opni


### Prerequisites
have a central cluster ready with Opni installed.
have a client cluster to install workload microservices and to ship traces to the central cluster. All the following steps should be done in the client cluster.

### Step 1

#### Manual Instrumentation
Install demo application
```
kubectl create -f microservices-demo.yaml
```

#### Auto Instrumentation (only work for python nodejs and java at this moment)
Install cert manager
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.2/cert-manager.yaml
```
install otel operator
```
kubectl apply -f https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml
```
install instrumentation
```
kubectl apply -f instrumantation.yaml
```
Install demo application
```
kubectl create -f boutique-auto-instrumantation.yaml
```

ref: https://github.com/open-telemetry/opentelemetry-operator
___

___

### Step 2
Install dataprepper
* set opensearch client url with Your Opni's opensearch external URL.
  * line 58, 86
* set opensearch password
  * lines 61, 89
```
kubectl create ns tracing
kubectl create -f data_prepper.yaml
```

___

### Step 3
Install opentelemetry-collector

```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm install opentelemetry-collector open-telemetry/opentelemetry-collector --values ot-collector-helm-values.yaml
```
* release name must be `opentelemetry-collector`

___

At this point, the demo is set up. 
Verify traces are being sent out to dataprepper by the otel-collector by checking the pod's logs. If you see logs like `2022-03-02T01:41:08.055Z	INFO	loggingexporter/logging_exporter.go:40	TracesExporter	{"#spans": 145}` you are good to go.

Head to the Opensearch Dashboards. Head to the Trace Analytics view from `Home -> OpenSearch Plugins -> Trace Analytics`. You should see traces populate here

___

![Opensearch trace analytics](https://github.com/sanjay920/ot-demo/blob/main/opensearch_trace_analytics.png)
