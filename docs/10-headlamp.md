# Install Headlamp

---

```yaml
ingress:
  enabled: true
  ingressClassName: nginx
  hosts:
    - host: headlamp.example.com
      paths:
        - path: /
          type: ImplementationSpecific
```

```shell
helm repo add headlamp https://kubernetes-sigs.github.io/headlamp/
helm install headlamp headlamp/headlamp --version 0.43.0 --create-namespace -n kube-system -f values.yaml
```

![01.png](../images/docs/10-headlamp/01.png)

![02.png](../images/docs/10-headlamp/02.png)

![03.png](../images/docs/10-headlamp/03.png)

![04.png](../images/docs/10-headlamp/04.png)

![05.png](../images/docs/10-headlamp/05.png)

![06.png](../images/docs/10-headlamp/06.png)

![07.png](../images/docs/10-headlamp/07.png)

![08.png](../images/docs/10-headlamp/08.png)

> update windows hosts (C:\Windows\System32\drivers\etc\hosts)

![09.png](../images/docs/10-headlamp/09.png)

![10.png](../images/docs/10-headlamp/10.png)

```shell
kubectl create token headlamp --namespace kube-system
```

![11.png](../images/docs/10-headlamp/11.png)

![12.png](../images/docs/10-headlamp/12.png)
