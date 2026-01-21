# OpenTelemetry Operator based onboarding flow

## Goals

* Implement K8s monitoring, including the Linux VMs of the Kubernetes nodes : metrics, logs, events
* Instrument Kubernetes workloads injecting OTel SDK auto-instrumentation (`inject-java`, `inject-dotnet`...) or configuration (`inject-sdk`)

## Getting Started

### Install OpenTelemetry Collector and Operator

Install the OpenTelemetry Collector and Operator on your Kubernetes cluster using the [OpenTelemetry Kube Stack Helm Chart](https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-kube-stack).

#### Prerequisites

The installation script will prompt you for the following information:

* **Grafana Cloud Credentials**: Your Grafana Cloud instance ID, OTLP endpoint URL, and API key
* **Kubernetes Cluster Name**: The name of your Kubernetes cluster (e.g., from AWS EKS, Azure AKS, or Google GKE)
* **Deployment Environment**: The environment name for your cluster (e.g., 'production', 'staging', or 'development')

#### Run the Installation Script

```console
$ ./install-otel-kube-stack-chart
```

#### Installation Characteristics
* OpenTelemetry Collector:
   * Installed as a daemonset on each Kubernetes node, in the Kubernetes namespace `opentelemetry-operator-system`
   * Send telemetry to Grafana Cloud
   * Collects:
     * Receive OTLP traces, metrics, and logs
     * Kubernetes cluster metrics and events
     * Kubernetes pod logs and metrics (ie Kubelet  metrics)
     * Kubernetes node host metrics
     * Workload metrics of Kubernetes pods annotated with the `io.opentelemetry.discovery.[metrics|logs]` annotations (see [OpenTelemetry Collector Receiver Creator Receiver](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/receivercreator))
  * Internal telemetry configured to send metrics and logs to Grafana Cloud
* OpenTelemetry Operator: 
   * Installed in the Kubernetes namespace `opentelemetry-operator-system`
   * Instrumentation CRD with
      * OTLP endpoint: the URL of the OTLP endpoint of the daemon collectors: `http://opentelemetry-stack-daemon-collector.opentelemetry-operator-system.svc.cluster.local:4318`
      * Sensible defaults to get the best of Grafana Cloud
* Grafana Cloud Credentials deployed as a Kubernetes secret `grafana-cloud-auth` in the Kubernetes namespace `opentelemetry-operator-system`

### Enable Instrumentation of Workloads

#### Choose Your Instrumentation Strategy

**Auto-instrumentation** is the recommended approach for most applications as it requires minimal code changes:
* Ideal for Java, Node.js, .NET, and Python applications
* Uses the OpenTelemetry Operator to inject instrumentation automatically
* Reduces configuration overhead and improves consistency

**Manual instrumentation** may be necessary for certain scenarios:
* Some .NET applications with complex dependency injection
* Applications requiring custom instrumentation logic
* Languages without mature auto-instrumentation support

#### Auto-instrumentation Setup

Add the following pod annotation to enable auto-instrumentation:

```yaml
instrumentation.opentelemetry.io/inject-<<language>>: opentelemetry-operator-system/otel-instrumentation
```

Replace `<<language>>` with your application's language. Supported values include `java`, `dotnet`, `nodejs`, `python`, and others. See the [OpenTelemetry Operator documentation](https://github.com/open-telemetry/opentelemetry-operator?tab=readme-ov-file#opentelemetry-auto-instrumentation-injection) for the complete list.

#### Manual Instrumentation Setup

For manual instrumentation, it is recommended to enable configuration injection using the annotation:

```yaml
instrumentation.opentelemetry.io/inject-sdk: opentelemetry-operator-system/otel-instrumentation
```

The OpenTelemetry Operator will inject SDK configuration through pod environment variables, ensuring consistent resource attributes (`service.name`, `service.namespace`, `service.instance.id`, `service.version`, `deployment.environment.name`) across all telemetry streams, aligning with the [OpenTelemetry semantic conventions](https://opentelemetry.io/docs/specs/semconv/non-normative/k8s-attributes/).

If configuration injection is not possible, use in the configuration of OpenTelemetry SDKs the OTLP endpoint `http://opentelemetry-stack-daemon-collector.opentelemetry-operator-system.svc.cluster.local:4318` for `http/protobuf` or port `4317`for `grpc`.

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
* `service.instance.id`:
   * Let the OTel Operator and OTelCollector generate it not specifying anything
   * The pod annotation `resource.opentelemetry.io/service.instance.id` is for advanced users
* `service.version`: decreasing preference
   * Rely on the container image name tag if it's human readable (ie a semantic version)
      * Why: to reduce duplication and version tagging mgmt
* `deployment.environment.name`
   * It's valuable to set it in the ingestion pipeline in the collector so the K8s deployment manifest remains unchanged across deployment environments so don't define it as a pod annotation.

⚠️ In the OTel Collector, ensure that all telemetry is enriched by the [OTel Collector K8s Attributes Processor]([url](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/k8sattributesprocessor)) with `otel_annotations: true`

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
