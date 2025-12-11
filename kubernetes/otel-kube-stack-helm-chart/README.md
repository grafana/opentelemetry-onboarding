# OpenTelemetry Operator based onboarding flow

## Goals

* Implement K8s monitoring, including the Linux VMs of the Kubernetes nodes : metrics, logs, events
* Instrument Kubernetes workloads injecting OTel SDK auto-instrumentation (`inject-java`, `inject-dotnet`...) or configuration (`inject-sdk`)

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
