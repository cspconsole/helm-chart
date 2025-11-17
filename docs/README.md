# CSP Console Helm Chart

A Helm chart for deploying CSP Console application with report collector, report processor, and config provider services on Kubernetes.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- PV provisioner support in the underlying infrastructure (if persistence is required)

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
helm install my-release .
```

The command deploys CSP Console on the Kubernetes cluster with the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
helm uninstall my-release
```

## Parameters

### Global Parameters

| Name | Description | Value |
|------|-------------|-------|
| `global.namespace` | Kubernetes namespace | `cspconsole` |
| `global.ingress.enabled` | Enable ingress | `true` |
| `global.ingress.ingressClassName` | Ingress class name | `api-nginx` |
| `global.ingress.hosts` | Ingress hosts | `["api.cspconsole.com"]` |
| `global.ingress.tls.hosts` | TLS hosts | `["*.api.cspconsole.com"]` |

### Global External Services (Third-party Dependencies)

| Name | Description | Value |
|------|-------------|-------|
| `global.externalServices.elasticsearch.tls.enabled` | Enable Elasticsearch TLS certificate mounting | `true` |
| `global.externalServices.elasticsearch.tls.secretName` | Secret name for Elasticsearch CA cert (defaults to chart fullname) | `""` |
| `global.externalServices.elasticsearch.tls.secretKey` | Secret key containing Elasticsearch CA certificate | `searchCaCert` |
| `global.externalServices.rabbitmq.tls.enabled` | Enable RabbitMQ TLS certificate mounting | `true` |
| `global.externalServices.rabbitmq.tls.secretName` | Secret name for RabbitMQ CA cert (defaults to chart fullname) | `""` |
| `global.externalServices.rabbitmq.tls.secretKey` | Secret key containing RabbitMQ CA certificate | `queueCaCert` |

### Report Collector Parameters

| Name | Description | Value |
|------|-------------|-------|
| `reportCollector.hpa.minReplicas` | Minimum number of replicas | `2` |
| `reportCollector.hpa.maxReplicas` | Maximum number of replicas | `10` |
| `reportCollector.hpa.averageUtilization` | Target CPU utilization percentage | `70` |
| `reportCollector.terminationGracePeriod` | Termination grace period in seconds | `30` |
| `reportCollector.secretName` | Secret name for external service credentials | `report-collector` |
| `reportCollector.deployment.type` | Deployment strategy type | `RollingUpdate` |
| `reportCollector.deployment.maxSurge` | Maximum surge during updates | `1` |
| `reportCollector.deployment.maxUnavailable` | Maximum unavailable during updates | `1` |
| `reportCollector.podDisruptionBudget.minAvailable` | Minimum available pods | `1` |
| `reportCollector.image.repository` | Image repository | `cspconsole/report-collector` |
| `reportCollector.image.tag` | Image tag | `latest` |
| `reportCollector.image.pullPolicy` | Image pull policy | `Always` |
| `reportCollector.service.type` | Service type | `ClusterIP` |
| `reportCollector.service.ports.https.port` | Service port | `80` |
| `reportCollector.service.ports.https.targetPort` | Container port | `8000` |
| `reportCollector.resources` | Resource limits and requests | `{}` |
| `reportCollector.nodeSelector` | Node selector | `{"agentpool": "dynamicpoolb"}` |
| `reportCollector.tolerations` | Tolerations | `[]` |
| `reportCollector.affinity` | Affinity rules | `{}` |

### Report Processor Parameters

| Name | Description | Value |
|------|-------------|-------|
| `reportProcessor.hpa.minReplicas` | Minimum number of replicas | `5` |
| `reportProcessor.hpa.maxReplicas` | Maximum number of replicas | `40` |
| `reportProcessor.hpa.averageUtilization` | Target CPU utilization percentage | `60` |
| `reportProcessor.terminationGracePeriod` | Termination grace period in seconds | `30` |
| `reportProcessor.secretName` | Secret name for external service credentials | `report-processor` |
| `reportProcessor.deployment.type` | Deployment strategy type | `RollingUpdate` |
| `reportProcessor.deployment.maxSurge` | Maximum surge during updates | `1` |
| `reportProcessor.deployment.maxUnavailable` | Maximum unavailable during updates | `1` |
| `reportProcessor.podDisruptionBudget.minAvailable` | Minimum available pods | `1` |
| `reportProcessor.image.repository` | Image repository | `cspconsole/report-processor` |
| `reportProcessor.image.tag` | Image tag | `latest` |
| `reportProcessor.image.pullPolicy` | Image pull policy | `Always` |
| `reportProcessor.logger.level` | Logger level (error, warn, info, debug) | `error` |
| `reportProcessor.nodeSelector` | Node selector | `{"agentpool": "dynamicpoolb"}` |
| `reportProcessor.tolerations` | Tolerations | `[]` |
| `reportProcessor.affinity` | Affinity rules | `{}` |

### Config Provider Parameters

| Name | Description | Value |
|------|-------------|-------|
| `configProvider.hpa.minReplicas` | Minimum number of replicas | `1` |
| `configProvider.hpa.maxReplicas` | Maximum number of replicas | `10` |
| `configProvider.hpa.averageUtilization` | Target CPU utilization percentage | `70` |
| `configProvider.terminationGracePeriod` | Termination grace period in seconds | `30` |
| `configProvider.secretName` | Secret name for external service credentials | `config-provider` |
| `configProvider.deployment.type` | Deployment strategy type | `RollingUpdate` |
| `configProvider.deployment.maxSurge` | Maximum surge during updates | `1` |
| `configProvider.deployment.maxUnavailable` | Maximum unavailable during updates | `1` |
| `configProvider.podDisruptionBudget.minAvailable` | Minimum available pods | `1` |
| `configProvider.image.repository` | Image repository | `cspconsole/config-provider` |
| `configProvider.image.tag` | Image tag | `latest` |
| `configProvider.image.pullPolicy` | Image pull policy | `Always` |
| `configProvider.service.type` | Service type | `ClusterIP` |
| `configProvider.service.ports.https.port` | Service port | `80` |
| `configProvider.service.ports.https.targetPort` | Container port | `8000` |
| `configProvider.resources` | Resource limits and requests | `{}` |
| `configProvider.nodeSelector` | Node selector | `{"agentpool": "dynamicpoolb"}` |
| `configProvider.tolerations` | Tolerations | `[]` |
| `configProvider.affinity` | Affinity rules | `{}` |

## Configuration and Installation Details

### Custom Configuration

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example:

```bash
helm install my-release \
  --set reportCollector.hpa.minReplicas=3 \
  --set reportProcessor.logger.level=debug \
  --set global.ingress.hosts[0]=myapp.example.com \
  .
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example:

```bash
helm install my-release -f values.yaml .
```

### Ingress

This chart provides support for Ingress resources. If you have an ingress controller installed on your cluster, you can configure the ingress settings in the `global.ingress` section.

### High Availability

The chart configures Horizontal Pod Autoscaler (HPA) and Pod Disruption Budgets (PDB) for all services to ensure high availability.

### External Services TLS/SSL Configuration

The chart supports mounting TLS/SSL certificates for external services like Elasticsearch and RabbitMQ. These are configured globally and can be enabled/disabled:

- **Elasticsearch**: Mounts CA certificate for secure connections to Elasticsearch
- **RabbitMQ**: Mounts CA certificate for secure connections to RabbitMQ/Queue services

To disable certificate mounting for a service:

```bash
helm install my-release \
  --set global.externalServices.elasticsearch.tls.enabled=false \
  .
```

### Logger Configuration

The report processor supports configurable logger levels through `reportProcessor.logger.level`. Available options:
- `error` (default): Only log errors
- `warn`: Log warnings and errors
- `info`: Log informational messages, warnings, and errors
- `debug`: Log all messages including debug information

Note: When using `debug` mode, increase memory allocation to 192Mi instead of the default 150Mi.

## Upgrading

To upgrade the `my-release` deployment:

```bash
helm upgrade my-release .
```

## License

Apache-2.0
