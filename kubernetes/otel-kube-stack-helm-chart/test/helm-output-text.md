


## CURRENT

```console
Release "opentelemetry-stack" does not exist. Installing it now.
NAME: opentelemetry-stack
LAST DEPLOYED: Thu Jan 29 18:07:45 2026
NAMESPACE: opentelemetry-operator-system
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
NOTES:
OpenTelemetry Kube Stack Helm Chart installation completed successfully!

With the features you've enabled, you can:

* Send OTLP to 'opentelemetry-stack-daemon-collector.opentelemetry-operator-system.svc.cluster.local:4317' (grpc)
* Send OTLP to 'opentelemetry-stack-daemon-collector.opentelemetry-operator-system.svc.cluster.local:4318' (http)
* Annotate your pods with: 'instrumentation.opentelemetry.io/inject-<<language>>: opentelemetry-operator-system/otel-instrumentation' to auto-instrument them


Happy observing!
```

## PROPOSAL

```console
Release "opentelemetry-stack" does not exist. Installing it now.
NAME: opentelemetry-stack
LAST DEPLOYED: Thu Jan 29 18:07:45 2026
NAMESPACE: opentelemetry-operator-system
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
NOTES:
OpenTelemetry Kube Stack Helm Chart installation completed successfully!

With the features you've enabled, you can:

* **Auto-instrument your applications** using the OpenTelemetry Operator:
   Add this annotation to your pod specs to inject the OTel SDK and automatic configuration:
   `instrumentation.opentelemetry.io/inject-<language>: opentelemetry-operator-system/otel-instrumentation`
   Replace `<language>` with: `dotnet`, `go`, `java`, `nodejs`, `python`, etc.

* **Use manual instrumentation with configuration injection**:
   Bundle the OTel SDK in your containers, then add this annotation for configuration injection:
   `instrumentation.opentelemetry.io/inject-sdk: opentelemetry-operator-system/otel-instrumentation`

* **Manually instrument with OTel SDKs** and configure endpoints directly:
   - **gRPC**: `opentelemetry-stack-daemon-collector.opentelemetry-operator-system.svc.cluster.local:4317`
   - **HTTP/Protobuf**: `opentelemetry-stack-daemon-collector.opentelemetry-operator-system.svc.cluster.local:4318`

For more details, visit:
https://opentelemetry.io/docs/platforms/kubernetes/operator/automatic/#add-annotations-to-existing-deployments

Happy observing!
```