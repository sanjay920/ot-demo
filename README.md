# ot-demo

### Step 1
Install demo application
```
kubectl create -f microservices-demo.yaml
```

___

### Step 2
Install opensearch
https://github.com/rancher-sandbox/opni-opensearch-operator

* [cluster.yaml](https://raw.githubusercontent.com/sanjay920/ot-demo/main/cluster.yaml)
* [os-dashboard.yaml](https://raw.githubusercontent.com/sanjay920/ot-demo/main/os-dashboard.yaml)

```
kubectl create -k opni-opensearch-operator/config/default
kubectl create -f cluster.yaml
kubectl create -f os-dashboard.yaml
```

Take note of the `name` of the opensearch cluster you configure

___

### Step 3
Install dataprepper
* set opensearch client url with your cluster's name
  * line 58, 86
* set opensearch password
  * lines 61, 89
```
kubectl create ns tracing
kubectl create -f data_prepper.yaml
```

___

### Step 4
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
