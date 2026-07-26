# How to Install Dashboard Headlamp

###### * Helm installation can be performed on any node; we take the k8s-master node as an example here.

## Installation

> Since kubernetes-dashboard is now archived and no longer maintained. We will adopt the officially recommended headlamp here.

```yaml
# values.yaml
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

![01.png](../../images/docs/infra/13-headlamp/01.png)

![02.png](../../images/docs/infra/13-headlamp/02.png)

![03.png](../../images/docs/infra/13-headlamp/03.png)

---

## Verification

> Configure forwarding rules for virtual machines to allow access from the Windows host.

![04.png](../../images/docs/infra/13-headlamp/04.png)

![05.png](../../images/docs/infra/13-headlamp/05.png)

![06.png](../../images/docs/infra/13-headlamp/06.png)

![07.png](../../images/docs/infra/13-headlamp/07.png)

![08.png](../../images/docs/infra/13-headlamp/08.png)

![09.png](../../images/docs/infra/13-headlamp/09.png)

> Add the host name to the C:\Windows\System32\drivers\etc\hosts file.

![10.png](../../images/docs/infra/13-headlamp/10.png)

![11.png](../../images/docs/infra/13-headlamp/11.png)

```shell
kubectl create token headlamp --namespace kube-system
```

![12.png](../../images/docs/infra/13-headlamp/12.png)

![13.png](../../images/docs/infra/13-headlamp/13.png)
