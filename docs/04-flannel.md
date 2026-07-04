# Install Flannel Component

---

```shell
helm repo add flannel https://flannel-io.github.io/flannel
helm install flannel flannel/flannel --version 0.28.5 --create-namespace -n flannel
```

![01.png](../images/docs/04-flannel/01.png)

```shell
kubectl get po -n flannel
```

![02.png](../images/docs/04-flannel/02.png)

![03.png](../images/docs/04-flannel/03.png)