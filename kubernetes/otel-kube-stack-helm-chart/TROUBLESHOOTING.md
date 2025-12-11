# Troubleshooting

## Kubernetes Cert Manager installation

Optional component, strongly recommended in production

```shell
# Wait for cert-manager to be ready
kubectl wait --for=condition=ready pod \
  -l app.kubernetes.io/instance=cert-manager \
  --namespace cert-manager \
  --timeout=300s
```

## OpenTelemetry Operator

The OpenTelemetry Operator is the orchestrator of all OpenTelemetry components installed by the OpenTelemetry KubStack Helm Chart.

* Wait for OpenTelemetry operator to be ready
  
```shell
# Wait for operator to be ready
kubectl wait --for=condition=ready pod \
  -l app.kubernetes.io/name=opentelemetry-operator \
  --namespace opentelemetry-operator-system \
  --timeout=300s
```

Example output

```
pod/opentelemetry-stack-opentelemetry-operator-578d5cb868-hxqps condition met
```

* Verify installation of the OpenTelemetry Operator pods:

```shell
# Verify pods
kubectl get pods --namespace opentelemetry-operator-system
```

Example output

```
NAME                                                          READY   STATUS    RESTARTS        AGE
opentelemetry-stack-daemon-collector-mk7rh                    1/1     Running   2 (5h32m ago)   13h
opentelemetry-stack-opentelemetry-operator-578d5cb868-hxqps   2/2     Running   4 (5h32m ago)   13h
```

* Verify installation of the OpenTelemetry Operator CRDs:


```shell
# Verify CRDs
kubectl get crd | grep opentelemetry
```

Example output
```
instrumentations.opentelemetry.io           2025-12-11T07:59:17Z
opampbridges.opentelemetry.io               2025-12-11T07:59:17Z
opentelemetrycollectors.opentelemetry.io    2025-12-11T07:59:17Z
targetallocators.opentelemetry.io           2025-12-11T07:59:17Z
```

* Verify installation of the OpenTelemetry Operator ConfigMaps:

```shell
# Verify configmap derived from  opentelemetrycollectors CRDs to configure collectors
kubectl get configmaps --namespace opentelemetry-operator-system | grep opentelemetry
```

Example output

```
opentelemetry-stack-daemon-collector-ff2e9a38   1      13h
```


* Verify the logs of the OpenTelemetry Operator

Example OpenTelemetry Operator logs:

```
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","message":"Starting the OpenTelemetry Operator","opentelemetry-operator":"0.138.0","build-date":"2025-10-31T18:30:48Z","go-version":"go1.25.3","go-arch":"arm64","go-os":"linux","feature-gates":"-operand.networkpolicy,operator.collector.default.config,-operator.golang.flags,-operator.networkpolicy,operator.sidecarcontainers.native,-operator.targetallocator.fallbackstrategy,-operator.targetallocator.mtls","config":{"auto-instrumentation-apache-httpd-image":"ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-apache-httpd:1.0.4","auto-instrumentation-dot-net-image":"ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-dotnet:1.2.0","auto-instrumentation-go-image":"ghcr.io/open-telemetry/opentelemetry-go-instrumentation/autoinstrumentation-go:v0.22.1","auto-instrumentation-java-image":"ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-java:1.33.6","auto-instrumentation-nginx-image":"ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-apache-httpd:1.0.4","auto-instrumentation-node-js-image":"ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-nodejs:0.64.1","auto-instrumentation-python-image":"ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-python:0.59b0","cert-manager-availability":"0","collector-availability":"0","collector-configmap-entry":"collector.yaml","collector-image":"otel/opentelemetry-collector-contrib:0.138.0","create-rbac-permissions":"0","create-service-monitor-operator-metrics":"false","enable-apache-httpd-instrumentation":"true","enable-cr-metrics":"false","enable-dot-net-auto-instrumentation":"true","enable-go-auto-instrumentation":"false","enable-java-auto-instrumentation":"true","enable-leader-election":"true","enable-multi-instrumentation":"true","enable-nginx-auto-instrumentation":"false","enable-node-js-auto-instrumentation":"true","enable-python-auto-instrumentation":"true","enable-webhooks":"true","fips-disabled-components":"uppercase","health-probe-addr":":8081","ignore-missing-collector-crds":"false","metrics-addr":"0.0.0.0:8080","opampbridge-availability":"0","open-shift-routes-availability":"1","openshift-create-dashboard":"false","operator-op-amp-bridge-configmap-entry":"remoteconfiguration.yaml","operatoropampbridge-image":"ghcr.io/open-telemetry/opentelemetry-operator/operator-opamp-bridge:0.138.0","pprof-addr":"","prometheus-cr-availability":"0","target-allocator-availability":"0","target-allocator-configmap-entry":"targetallocator.yaml","targetallocator-image":"ghcr.io/open-telemetry/opentelemetry-operator/target-allocator:0.138.0","webhook-port":"9443"}}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"setup","message":"the env var WATCH_NAMESPACE isn't set, watching all namespaces"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"setup","message":"Prometheus CRDs are installed, adding to scheme."}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"setup","message":"Openshift CRDs are not installed, skipping adding to scheme."}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"setup","message":"Cert-Manager is not available to the operator, skipping adding to scheme."}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"setup","message":"OpenTelemetryCollectorCRDSs are available to the operator"}
{"level":"DPANIC","timestamp":"2025-12-11T16:03:46Z","logger":"setup","message":"odd number of arguments passed as key-value pairs for logging","ignored key":true,"stacktrace":"main.main\n\t./main.go:253\nruntime.main\n\truntime/proc.go:285"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"setup","message":"Native sidecar"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Registering a mutating webhook","GVK":"opentelemetry.io/v1beta1, Kind=OpenTelemetryCollector","path":"/mutate-opentelemetry-io-v1beta1-opentelemetrycollector"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/mutate-opentelemetry-io-v1beta1-opentelemetrycollector"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Registering a validating webhook","GVK":"opentelemetry.io/v1beta1, Kind=OpenTelemetryCollector","path":"/validate-opentelemetry-io-v1beta1-opentelemetrycollector"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/validate-opentelemetry-io-v1beta1-opentelemetrycollector"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/convert"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Conversion webhook enabled","GVK":"opentelemetry.io/v1beta1, Kind=OpenTelemetryCollector"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Registering a mutating webhook","GVK":"opentelemetry.io/v1alpha1, Kind=TargetAllocator","path":"/mutate-opentelemetry-io-v1alpha1-targetallocator"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/mutate-opentelemetry-io-v1alpha1-targetallocator"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Registering a validating webhook","GVK":"opentelemetry.io/v1alpha1, Kind=TargetAllocator","path":"/validate-opentelemetry-io-v1alpha1-targetallocator"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/validate-opentelemetry-io-v1alpha1-targetallocator"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Registering a mutating webhook","GVK":"opentelemetry.io/v1alpha1, Kind=Instrumentation","path":"/mutate-opentelemetry-io-v1alpha1-instrumentation"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/mutate-opentelemetry-io-v1alpha1-instrumentation"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Registering a validating webhook","GVK":"opentelemetry.io/v1alpha1, Kind=Instrumentation","path":"/validate-opentelemetry-io-v1alpha1-instrumentation"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/validate-opentelemetry-io-v1alpha1-instrumentation"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/mutate-v1-pod"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Registering a mutating webhook","GVK":"opentelemetry.io/v1alpha1, Kind=OpAMPBridge","path":"/mutate-opentelemetry-io-v1alpha1-opampbridge"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/mutate-opentelemetry-io-v1alpha1-opampbridge"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.builder","message":"Registering a validating webhook","GVK":"opentelemetry.io/v1alpha1, Kind=OpAMPBridge","path":"/validate-opentelemetry-io-v1alpha1-opampbridge"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Registering webhook","path":"/validate-opentelemetry-io-v1alpha1-opampbridge"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"setup","message":"starting manager"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","message":"starting server","name":"health probe","addr":"[::]:8081"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.metrics","message":"Starting metrics server"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.metrics","message":"Serving metrics server","bindAddress":"0.0.0.0:8080","secure":false}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Starting webhook server"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.certwatcher","message":"Updated current TLS certificate"}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.webhook","message":"Serving webhook server","host":"","port":9443}
{"level":"INFO","timestamp":"2025-12-11T16:03:46Z","logger":"controller-runtime.certwatcher","message":"Starting certificate poll+watcher","interval":10}
I1211 16:03:46.928835       1 leaderelection.go:257] attempting to acquire leader lease opentelemetry-operator-system/9f7554c3.opentelemetry.io...
I1211 16:06:41.073627       1 leaderelection.go:271] successfully acquired lease opentelemetry-operator-system/9f7554c3.opentelemetry.io
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","logger":"instrumentation-upgrade","message":"looking for managed Instrumentation instances to upgrade"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.ServiceMonitor"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.Deployment"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.ConfigMap"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.DaemonSet"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.ServiceAccount"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1.PodMonitor"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.StatefulSet"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1beta1.OpenTelemetryCollector"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opampbridge","controllerGroup":"opentelemetry.io","controllerKind":"OpAMPBridge","source":"kind source: *v1.Deployment"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1beta1.OpenTelemetryCollector"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opampbridge","controllerGroup":"opentelemetry.io","controllerKind":"OpAMPBridge","source":"kind source: *v1alpha1.OpAMPBridge"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1.Service"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.Service"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1.ConfigMap"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.PodDisruptionBudget"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1.ServiceAccount"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1alpha1.TargetAllocator"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opampbridge","controllerGroup":"opentelemetry.io","controllerKind":"OpAMPBridge","source":"kind source: *v1.ConfigMap"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1beta1.OpenTelemetryCollector"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.PodMonitor"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1.ServiceMonitor"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1.PodDisruptionBudget"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1.Deployment"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.NetworkPolicy"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v2.HorizontalPodAutoscaler"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","source":"kind source: *v1alpha1.TargetAllocator"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opampbridge","controllerGroup":"opentelemetry.io","controllerKind":"OpAMPBridge","source":"kind source: *v1.Service"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","source":"kind source: *v1.Ingress"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting EventSource","controller":"opampbridge","controllerGroup":"opentelemetry.io","controllerKind":"OpAMPBridge","source":"kind source: *v1.ServiceAccount"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting Controller","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting workers","controller":"targetallocator","controllerGroup":"opentelemetry.io","controllerKind":"TargetAllocator","worker count":1}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting Controller","controller":"opampbridge","controllerGroup":"opentelemetry.io","controllerKind":"OpAMPBridge"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting workers","controller":"opampbridge","controllerGroup":"opentelemetry.io","controllerKind":"OpAMPBridge","worker count":1}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting Controller","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector"}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","message":"Starting workers","controller":"opentelemetrycollector","controllerGroup":"opentelemetry.io","controllerKind":"OpenTelemetryCollector","worker count":1}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","logger":"KubeAPIWarningLogger","message":"unknown field \"spec.apacheHttpd.volumeClaimTemplate.metadata.creationTimestamp\""}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","logger":"KubeAPIWarningLogger","message":"unknown field \"spec.dotnet.volumeClaimTemplate.metadata.creationTimestamp\""}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","logger":"KubeAPIWarningLogger","message":"unknown field \"spec.go.volumeClaimTemplate.metadata.creationTimestamp\""}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","logger":"KubeAPIWarningLogger","message":"unknown field \"spec.java.volumeClaimTemplate.metadata.creationTimestamp\""}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","logger":"KubeAPIWarningLogger","message":"unknown field \"spec.nginx.volumeClaimTemplate.metadata.creationTimestamp\""}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","logger":"KubeAPIWarningLogger","message":"unknown field \"spec.nodejs.volumeClaimTemplate.metadata.creationTimestamp\""}
{"level":"INFO","timestamp":"2025-12-11T16:06:41Z","logger":"KubeAPIWarningLogger","message":"unknown field \"spec.python.volumeClaimTemplate.metadata.creationTimestamp\""}
```


* Verify the logs of the OpenTelemetry Collector

```
2025-12-11T16:03:47.854Z    info    builders/extension.go:50    Development component. May change in the future.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_leader_elector/k8s_objects", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.857Z    info    builders/extension.go:50    Development component. May change in the future.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_leader_elector/k8s_cluster", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.867Z    info    service@v0.138.0/service.go:222    Starting otelcol-contrib...    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "Version": "0.138.0", "NumCPU": 8}
2025-12-11T16:03:47.867Z    info    extensions/extensions.go:41    Starting extensions...    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}}
2025-12-11T16:03:47.869Z    info    extensions/extensions.go:45    Extension is starting...    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_observer", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.876Z    info    extensions/extensions.go:62    Extension started.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_observer", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.876Z    info    extensions/extensions.go:45    Extension is starting...    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "file_storage", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.876Z    info    extensions/extensions.go:62    Extension started.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "file_storage", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.876Z    info    extensions/extensions.go:45    Extension is starting...    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "basicauth/grafana_cloud", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.876Z    info    extensions/extensions.go:62    Extension started.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "basicauth/grafana_cloud", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.876Z    info    extensions/extensions.go:45    Extension is starting...    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_leader_elector/k8s_cluster", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.876Z    info    k8sleaderelector@v0.138.0/extension.go:93    Starting k8s leader elector with UUID    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_leader_elector/k8s_cluster", "otelcol.component.kind": "extension", "UUID": "eab141e0-1304-44ff-b0f5-6d8cb61372b1"}
2025-12-11T16:03:47.885Z    info    extensions/extensions.go:62    Extension started.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_leader_elector/k8s_cluster", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.885Z    info    extensions/extensions.go:45    Extension is starting...    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_leader_elector/k8s_objects", "otelcol.component.kind": "extension"}
2025-12-11T16:03:47.885Z    info    k8sleaderelector@v0.138.0/extension.go:93    Starting k8s leader elector with UUID    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_leader_elector/k8s_objects", "otelcol.component.kind": "extension", "UUID": "f02a8321-21d5-4c70-85a6-1d96149dd2ee"}
2025-12-11T16:03:47.885Z    info    extensions/extensions.go:62    Extension started.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_leader_elector/k8s_objects", "otelcol.component.kind": "extension"}
I1211 16:03:47.895509       1 leaderelection.go:257] attempting to acquire leader lease opentelemetry-operator-system/k8s.cluster.receiver.opentelemetry.io...
I1211 16:03:47.895546       1 leaderelection.go:257] attempting to acquire leader lease opentelemetry-operator-system/k8s.objects.receiver.opentelemetry.io...
2025-12-11T16:03:47.895Z    info    internal/resourcedetection.go:137    began detecting resource information    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "resourcedetection/env", "otelcol.component.kind": "processor", "otelcol.pipeline.id": "traces", "otelcol.signal": "traces"}
2025-12-11T16:03:47.931Z    info    internal/resourcedetection.go:188    detected resource information    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "resourcedetection/env", "otelcol.component.kind": "processor", "otelcol.pipeline.id": "traces", "otelcol.signal": "traces", "resource": {"k8s.cluster.name":"my-k8s-cluster","k8s.node.name":"k3d-otel-onboarding-test-server-0","k8s.node.uid":"a65fbc7d-89d4-417d-80f7-33021a895ea1"}}
2025-12-11T16:03:47.937Z    info    kube/client.go:205    k8s filtering    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sattributes", "otelcol.component.kind": "processor", "otelcol.pipeline.id": "metrics", "otelcol.signal": "metrics", "labelSelector": "", "fieldSelector": "spec.nodeName=k3d-otel-onboarding-test-server-0"}
2025-12-11T16:03:47.945Z    info    kube/client.go:205    k8s filtering    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sattributes", "otelcol.component.kind": "processor", "otelcol.pipeline.id": "traces", "otelcol.signal": "traces", "labelSelector": "", "fieldSelector": "spec.nodeName=k3d-otel-onboarding-test-server-0"}
2025-12-11T16:03:47.945Z    info    otlpreceiver@v0.138.0/otlp.go:121    Starting GRPC server    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "otlp", "otelcol.component.kind": "receiver", "endpoint": "[::]:4317"}
2025-12-11T16:03:47.953Z    info    otlpreceiver@v0.138.0/otlp.go:179    Starting HTTP server    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "otlp", "otelcol.component.kind": "receiver", "endpoint": "[::]:4318"}
2025-12-11T16:03:47.953Z    info    k8sclusterreceiver@v0.138.0/receiver.go:108    Starting k8sClusterReceiver with leader election    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_cluster", "otelcol.component.kind": "receiver", "otelcol.signal": "metrics"}
2025-12-11T16:03:47.966Z    info    kube/client.go:205    k8s filtering    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sattributes", "otelcol.component.kind": "processor", "otelcol.pipeline.id": "logs", "otelcol.signal": "logs", "labelSelector": "", "fieldSelector": "spec.nodeName=k3d-otel-onboarding-test-server-0"}
2025-12-11T16:03:47.969Z    info    adapter/receiver.go:41    Starting stanza receiver    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "filelog", "otelcol.component.kind": "receiver", "otelcol.signal": "logs"}
2025-12-11T16:03:48.142Z    info    fileconsumer/file.go:62    Resuming from previously known offset(s). 'start_at' setting is not applicable.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "filelog", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "component": "fileconsumer"}
2025-12-11T16:03:48.162Z    info    k8sobjectsreceiver@v0.138.0/receiver.go:130    registering the receiver in leader election    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sobjects", "otelcol.component.kind": "receiver", "otelcol.signal": "logs"}
2025-12-11T16:03:48.192Z    info    service@v0.138.0/service.go:245    Everything is ready. Begin running and processing data.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}}
2025-12-11T16:03:48.360Z    info    fileconsumer/file.go:261    Started watching file    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "filelog", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "component": "fileconsumer", "path": "/var/log/pods/kube-system_local-path-provisioner-774c6665dc-hh9j7_bca4e77b-ff13-4d3f-b1bb-4dc2c57ec6d6/local-path-provisioner/2.log"}
2025-12-11T16:03:48.360Z    info    fileconsumer/file.go:261    Started watching file    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "filelog", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "component": "fileconsumer", "path": "/var/log/pods/kube-system_metrics-server-7bfffcd44-srgq5_a800b1d2-d783-4e5f-adf6-b6953c73950f/metrics-server/2.log"}
I1211 16:04:04.623393       1 leaderelection.go:271] successfully acquired lease opentelemetry-operator-system/k8s.cluster.receiver.opentelemetry.io
2025-12-11T16:04:04.664Z    info    k8sclusterreceiver@v0.138.0/receiver.go:64    Starting shared informers and wait for initial cache sync.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_cluster", "otelcol.component.kind": "receiver", "otelcol.signal": "metrics"}
2025-12-11T16:04:04.766Z    info    k8sclusterreceiver@v0.138.0/receiver.go:85    Completed syncing shared informer caches.    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8s_cluster", "otelcol.component.kind": "receiver", "otelcol.signal": "metrics"}
I1211 16:04:05.697768       1 leaderelection.go:271] successfully acquired lease opentelemetry-operator-system/k8s.objects.receiver.opentelemetry.io
2025-12-11T16:04:05.699Z    info    k8sobjectsreceiver@v0.138.0/receiver.go:198    Started collecting    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sobjects", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "gvr": "events.k8s.io/v1, Resource=events", "mode": "watch", "namespaces": []}
2025-12-11T16:04:05.700Z    info    k8sobjectsreceiver@v0.138.0/receiver.go:143    Object Receiver started as leader    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sobjects", "otelcol.component.kind": "receiver", "otelcol.signal": "logs"}
2025-12-11T16:41:52.951Z    info    k8sobjectsreceiver@v0.138.0/receiver.go:386    received a 410, grabbing new resource version    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sobjects", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "data": {"Type":"ERROR","Object":{"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"The resourceVersion for the provided watch is too old.","reason":"Expired","code":410}}}
2025-12-11T18:19:19.650Z    info    k8sobjectsreceiver@v0.138.0/receiver.go:386    received a 410, grabbing new resource version    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sobjects", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "data": {"Type":"ERROR","Object":{"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"The resourceVersion for the provided watch is too old.","reason":"Expired","code":410}}}
2025-12-11T19:55:59.219Z    info    k8sobjectsreceiver@v0.138.0/receiver.go:386    received a 410, grabbing new resource version    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sobjects", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "data": {"Type":"ERROR","Object":{"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"The resourceVersion for the provided watch is too old.","reason":"Expired","code":410}}}
2025-12-11T20:28:00.574Z    info    k8sobjectsreceiver@v0.138.0/receiver.go:386    received a 410, grabbing new resource version    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sobjects", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "data": {"Type":"ERROR","Object":{"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"The resourceVersion for the provided watch is too old.","reason":"Expired","code":410}}}
2025-12-11T21:16:46.275Z    info    k8sobjectsreceiver@v0.138.0/receiver.go:386    received a 410, grabbing new resource version    {"resource": {"service.instance.id": "796ef5c9-c481-46b2-9b6c-274611762daf", "service.name": "otelcol-contrib", "service.version": "0.138.0"}, "otelcol.component.id": "k8sobjects", "otelcol.component.kind": "receiver", "otelcol.signal": "logs", "data": {"Type":"ERROR","Object":{"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"The resourceVersion for the provided watch is too old.","reason":"Expired","code":410}}}
```
