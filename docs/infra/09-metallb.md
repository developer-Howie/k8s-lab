# How to Install MetalLB

> **Note**: Helm installation can be performed on any node; we take the k8s-master node as an example here.

## Installation

```shell
# Add MetalLB Helm repository
helm repo add metallb https://metallb.github.io/metallb

# Install MetalLB load balancer
helm install metallb metallb/metallb --version 0.16.1 --create-namespace -n metallb
```

![01.png](../../images/docs/infra/09-metallb/01.png)

![02.png](../../images/docs/infra/09-metallb/02.png)

```yaml
# Configure MetalLB in L2 mode with an IP address pool for load balancing
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2-advertisement
  namespace: metallb
---
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: default-pool
  namespace: metallb
spec:
  addresses:
    - 192.168.47.200-192.168.47.220
```

---

## Verification

> **Note**: Create a Service of LoadBalancer type for testing purposes.

```yaml
# Create a Deployment and LoadBalancer Service to test MetalLB functionality
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:alpine
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-loadbalancer-svc
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

![03.png](../../images/docs/infra/09-metallb/03.png)

![04.png](../../images/docs/infra/09-metallb/04.png)