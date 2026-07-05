# How to Install CSI - Longhorn

###### * Helm installation can be performed on any node; we take the k8s-master node as an example here.

---

## Installation

> Since Longhorn is chosen as our CSI implementation, iscsi-initiator-utils must be installed in advance.

```shell
dnf -y install iscsi-initiator-utils
systemctl enable --now iscsid
```

![01.png](../images/docs/09-longhorn/01.png)

```shell
helm repo add longhorn https://charts.longhorn.io
helm install longhorn longhorn/longhorn --version 1.12.0 --create-namespace --namespace longhorn-system
```

![02.png](../images/docs/09-longhorn/02.png)

![03.png](../images/docs/09-longhorn/03.png)

![04.png](../images/docs/09-longhorn/04.png)

> Since the NFS protocol is selected, nfs-utils needs to be pre-installed.

```shell
dnf -y install nfs-utils
systemctl enable --now rpcbind
```

![05.png](../images/docs/09-longhorn/05.png)
![06.png](../images/docs/09-longhorn/06.png)

---

## Verification

> Create a PVC for testing purposes.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: shared-rwx-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: longhorn
  resources:
    requests:
      storage: 1Gi
```

![07.png](../images/docs/09-longhorn/07.png)

---

> Create two Pods. One Pod writes logs to files in the shared directory, while the other reads content from the files in the shared directory and prints them out.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: writer-pod
spec:
  containers:
    - name: writer
      image: busybox
      command: [ "/bin/sh", "-c" ]
      args:
        - while true;
          do
          echo "Hello from writer at $(date)" >> /shared/writer.log;
          sleep 10;
          done
      volumeMounts:
        - name: shared-vol
          mountPath: /shared
  volumes:
    - name: shared-vol
      persistentVolumeClaim:
        claimName: shared-rwx-pvc
---
apiVersion: v1
kind: Pod
metadata:
  name: reader-pod
spec:
  containers:
    - name: reader
      image: busybox
      command: [ "/bin/sh", "-c" ]
      args:
        - tail -f /shared/writer.log
      volumeMounts:
        - name: shared-vol
          mountPath: /shared
  volumes:
    - name: shared-vol
      persistentVolumeClaim:
        claimName: shared-rwx-pvc
```

![08.png](../images/docs/09-longhorn/08.png)
