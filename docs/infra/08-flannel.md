# How to Install CNI - Flannel

###### * Execute the following commands only on the k8s-master node.

## Installation

```shell
helm repo add flannel https://flannel-io.github.io/flannel
helm install flannel flannel/flannel --version 0.28.8 --create-namespace -n kube-flannel
```

![01.png](../../images/docs/infra/08-flannel/01.png)

![02.png](../../images/docs/infra/08-flannel/02.png)

> After installing the CNI plugin, flannel will create two network card

![03.png](../../images/docs/infra/08-flannel/03.png)

---

## Verification

> Create a Service of NodePort type for testing purposes.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
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
  name: nginx-node-port-svc
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

![04.png](../../images/docs/infra/08-flannel/04.png)

![05.png](../../images/docs/infra/08-flannel/05.png)
