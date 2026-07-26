# How to Install Metrics-server

> **Note**: Helm installation can be performed on any node; we take the k8s-master node as an example here.

## Installation

> **Note**: Due to network limitations, domestic mirror repositories are used here for metrics-server image.

```yaml
# values.yaml - Configure metrics-server with domestic mirror and insecure TLS settings
image:
  repository: registry.aliyuncs.com/google_containers/metrics-server
args:
  - --kubelet-insecure-tls
```

```shell
# Add metrics-server Helm repository
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/

# Install metrics-server with custom values
helm install metrics-server metrics-server/metrics-server --version 3.13.1 --create-namespace -n metrics-server -f values.yaml
```

![01.png](../../images/docs/infra/14-metrics-server/01.png)

![02.png](../../images/docs/infra/14-metrics-server/02.png)

---

## Verification

> **Note**: When accessing the Headlamp dashboard, you can view CPU and memory utilization rates.

![03.png](../../images/docs/infra/14-metrics-server/03.png)