# Install Nginx-Ingress

---

```shell
helm repo add nginx https://helm.nginx.com/stable
helm install nginx-ingress nginx/nginx-ingress --version 2.6.1 --create-namespace -n nginx-ingress
```

![img.png](../images/docs/07-nginx-ingress/img.png)

![img_2.png](../images/docs/07-nginx-ingress/img_2.png)

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
  name: nginx-clusterip-svc
spec:
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
spec:
  ingressClassName: nginx
  rules:
    - host: nginx.example.com
      http:
        paths:
          - backend:
              service:
                name: nginx-clusterip-svc
                port:
                  number: 80
            pathType: Prefix
            path: /
```

![img_3.png](../images/docs/07-nginx-ingress/img_3.png)

> Add host to /etc/hosts

![img_4.png](../images/docs/07-nginx-ingress/img_4.png)

![img_5.png](../images/docs/07-nginx-ingress/img_5.png)