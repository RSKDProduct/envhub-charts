# EnvHub Helm Charts

Helm charts for deploying EnvHub agents on Kubernetes clusters.

## Quick Start

### 1. Add the Helm repository

```bash
helm repo add envhub https://rskdproduct.github.io/envhub-charts
helm repo update
```

### 2. Generate an enrollment token

Go to **EnvHub Dashboard > Agents > Enroll Agent**, select an environment, and copy the token.

Each token is **single-use** and expires after 1 hour.

### 3. Install the agent

```bash
helm install envhub-agent envhub/envhub-agent \
  --set enrollmentToken="<YOUR_TOKEN>"
```

The agent will automatically connect to the EnvHub server (the server address is embedded in the token).

## Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `enrollmentToken` | Enrollment token from dashboard (required) | `""` |
| `image.repository` | Agent image | `ghcr.io/rskdproduct/envhub-agent` |
| `image.tag` | Image tag | `latest` |
| `serverAddress` | Override server address | `""` (auto from token) |
| `logLevel` | Log level (debug/info/warn/error) | `info` |
| `syncInterval` | Inventory sync interval (seconds) | `15` |
| `metricsPort` | Prometheus metrics port | `9090` |
| `standalone` | Run without server connection | `false` |
| `persistence.enabled` | Enable persistent storage | `true` |
| `persistence.size` | Storage size | `1Gi` |
| `persistence.storageClass` | Storage class | `""` (cluster default) |
| `resources.requests.cpu` | CPU request | `50m` |
| `resources.requests.memory` | Memory request | `64Mi` |
| `resources.limits.cpu` | CPU limit | `200m` |
| `resources.limits.memory` | Memory limit | `256Mi` |
| `serviceMonitor.enabled` | Enable Prometheus ServiceMonitor | `false` |
| `rbac.create` | Create RBAC resources | `true` |
| `namespace.create` | Create namespace | `true` |
| `namespace.name` | Namespace name | `envhub` |

## Upgrading

```bash
helm upgrade envhub-agent envhub/envhub-agent
```

## Uninstalling

```bash
helm uninstall envhub-agent
```

## What the agent collects

- **Kubernetes**: Nodes, pods, deployments, services, namespaces, statefulsets, daemonsets, jobs
- **Host**: CPU, memory, disk, network, OS info, system services
- **Docker**: Containers, images, networks (if Docker socket is available)

## RBAC Permissions

The agent creates a ClusterRole with:
- **Read-only**: nodes, namespaces, pods, services, endpoints, configmaps, deployments, replicasets, statefulsets, daemonsets, jobs, cronjobs, pods/log
- **Write**: pods (delete), deployments (update/patch/scale) — for remote actions from the dashboard
