# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

OpenTelemetry onboarding configurations for Grafana Cloud. Provides ready-to-use Helm chart values, collector configs, and Grafana provisioning (dashboards + alerts) to deploy OpenTelemetry on Kubernetes clusters and Linux hosts, exporting telemetry to Grafana Cloud via OTLP.

## Key Commands

### Generate rendered Helm examples

```bash
cd kubernetes/otel-kube-stack-helm-chart
make generate-examples
```

Uses `helm template` against each `examples/*/values.yaml` to produce rendered manifests in `examples/*/rendered/`. **Re-run this after any change to `values.yaml`** — the rendered examples are committed and must stay in sync.

### Test with k3d

```bash
cd kubernetes/otel-kube-stack-helm-chart
./test/create-k3d-cluster
cp .env.template .env  # fill in Grafana Cloud credentials
./install-otel-kube-stack-chart
# verify: kubectl get pods -n opentelemetry-operator-system
./test/delete-otel-kube-stack
./test/delete-k3d-cluster
```

Docker Desktop Kubernetes is not supported (breaks hostmetrics).

## Architecture

- **`kubernetes/otel-kube-stack-helm-chart/values.yaml`** — main configuration layered on top of the upstream [opentelemetry-kube-stack](https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-kube-stack) Helm chart. Configures a DaemonSet collector with presets (logs, host metrics, kubelet metrics, k8s attributes/events, cluster metrics) and auto-instrumentation for Java, Node.js, .NET, Python.
- **`kubernetes/otel-kube-stack-helm-chart/install-otel-kube-stack-chart`** — interactive install script that prompts for Grafana Cloud credentials, creates the namespace/secret, and runs `helm install`.
- **`linux/collector.yaml`** — standalone OTel Collector config for non-Kubernetes Linux hosts (hostmetrics, journald, OTLP receivers).
- **`grafana/provisioning/`** — Grafana alert rules and dashboard JSON for monitoring the OTel Collector itself.

## Dependency Management

Renovate is configured in `.github/renovate.json5` with a custom regex manager. To make a container image in `values.yaml` auto-updatable, add a `# renovate:` comment above the `image:` line:

```yaml
# renovate: datasource=docker depName=<name> packageName=<full-registry-path>
image: <full-registry-path>:<version>
```

The Java autoinstrumentation image uses the Grafana distribution (`us-docker.pkg.dev/grafanalabs-global/docker-grafana-opentelemetry-java-prod/grafana-opentelemetry-java`) rather than the upstream OpenTelemetry image.
