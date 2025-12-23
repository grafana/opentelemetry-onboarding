# OpenTelemetry Operator based onboarding flow

## Goals

* Implement K8s monitoring, including the Linux VMs of the Kubernetes nodes : metrics, logs, events
* Instrument Kubernetes workloads injecting OTel SDK auto-instrumentation (`inject-java`, `inject-dotnet`...) or configuration (`inject-sdk`)

## Getting Started


### Enable insrumentation of the workloads

* Auto-instrumentation is ecommended everywhere possible like Java. For some programming languages, it's better to do manual insyrumentation with bootstrap code (e.g. some .Net use cases)

* Manual instrumentation
   * It's strongly recommended to inject OTel configuration using the OTel Operator to ensure the `service.name`,  `service.namespace`, `service.instance.id` , `service.version` , `deployment.environment.name`... are consistent across all telemetry ingestion flows aligning on the [Specify resource attributes using Kubernetes annotations](https://opentelemetry.io/docs/specs/semconv/non-normative/k8s-attributes/) :  OTel SDK emitted telemetry, pod logs...
   * Use the K8s pod annotation `instrumentation.opentelemetry.io/inject-sdk: true` or `instrumentation.opentelemetry.io/inject-sdk: my_k8s_namespace/my_otel_instr_crd_name`
* Auto-instr
   * Use the K8s pod annotation `instrumentation.opentelemetry.io/inject-<< language >>: true` or `instrumentation.opentelemetry.io/inject-<< language >: my_k8s_namespace/my_otel_instr_crd_name`

Resource attributes best practices:
* `service.name`: decreasing preference
   * Pod annotation `resource.opentelemetry.io/service.name`
     * Why: because we want to specify the pod annotation `resource.opentelemetry.io/service.namespace` so it's more cohesive
   * Pod label `app.kubernetes.io/name`
   * Defaults to `k8s.deployment.name`
      * Warning: `prometheus_sd_discovery` doesn't discover `k8s.deployment.name` and Prometheus instrumentation often use `k8s.container.name`, causing miscorrelation
* `service.namespace`: decreasing preference
   * Pod annotation `resource.opentelemetry.io/service.namespace`
      * Why: to not default to `k8s.namespace.name` as it's not intuitive that changing the `k8s.namespace.name` would break alerts...
   * Defaults `k8s.namespace.name`
* `service.version`: decreasing preference
   * Rely on the container image name tag if it's human readable (ie a semantic version)
      * Why: to reduce duplication and version tagging mgmt
* `deployment.environment.name`
   * It's valuable to set it in the ingestion pipeline in the collector so the K8s deployment manifest remains unchanged across deployment environments so don't define it as a pod annotation.

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: fraud-detection
spec:
  ports:
    - name: http
      port: 8080
      targetPort: 8080
  selector:
    app.kubernetes.io/name: fraud-detection

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fraud-detection
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: fraud-detection
  template:
    metadata:
      annotations:
        resource.opentelemetry.io/service.name: fraud-detection
        resource.opentelemetry.io/service.namespace: ecommerce
        instrumentation.opentelemetry.io/inject-java: "opentelemetry-operator-system/otel-instrumentation"
      labels:
        app.kubernetes.io/name: fraud-detection
    spec:
      containers:
        - name: fraud-detection
          env: {}
          image: docker.io/.../webshop-fraud-detection:latest
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
      restartPolicy: Always
```

### Define instrumentation in the K8s deplyoment manifests (recommended)

Add pod annotation to instruct the OpenTlemetry to 


## Implementation

* Setup the OTel Operator in the K8s namespace `opentelemetry-operator-system`
* Setup OTel Collectors in the K8s namespace `opentelemetry-operator-system`
   * to
     * Collector pod metrics & logs
     * Collector K8s cluster metrics & events
     * Collect host metrics
  * deployed as daemonset with a leader election for singletons (eg K8s cluster metrics & events).

See https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-kube-stack



## Development and Testing

See [test](test) folder.
