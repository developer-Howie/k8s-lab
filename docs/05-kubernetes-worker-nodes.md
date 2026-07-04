# Join Cluster

```shell
kubeadm join 192.168.47.100:6443 \
    --token ocbnte.qcxsc9p6kmyjddvv \
	--discovery-token-ca-cert-hash sha256:f22a0de41e0e9164bd57d78c30d2a8ca30c5973114746926de38c41c1e4ec5a3 \
	--cri-socket unix:///var/run/cri-dockerd.sock
```

![img.png](../images/docs/05-kubernetes-worker-nodes/01.png)

![img_1.png](../images/docs/05-kubernetes-worker-nodes/02.png)

---

> Test

```yaml
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
  name: nginx-node-port
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

![img_2.png](../images/docs/05-kubernetes-worker-nodes/03.png)

![img_3.png](../images/docs/05-kubernetes-worker-nodes/04.png)