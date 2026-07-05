# How to Install Kubernetes Control Plane Node

###### * Unless otherwise specified, all commands shall be executed on all three nodes simultaneously.

---

## Prepare

### 0.1 Write hostname into /etc/hosts file

![01.png](../images/docs/03-kubernetes-control-plane/01.png)

![02.png](../images/docs/03-kubernetes-control-plane/02.png)

### 0.2 Install `wget` command

```shell
dnf -y install wget
```

![03.png](../images/docs/03-kubernetes-control-plane/03.png)

### 0.3 Shutdown firewall

```shell
systemctl status firewalld
systemctl stop firewalld
systemctl disable firewalld
systemctl status firewalld
```

![04.png](../images/docs/03-kubernetes-control-plane/04.png)

---

## Environment configuration

### 1.1 Shutdown swap

```shell
swapon --show
swapoff -a
sed -i '/swap/s/^/#/' /etc/fstab # Permanently disable to prevent restoration after VM reboot
swapon --show
```

![05.png](../images/docs/03-kubernetes-control-plane/05.png)

### 1.2 Set SELinux to permissive mode

```shell
getenforce
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config # Permanently disable to prevent restoration after VM reboot
getenforce
```

![06.png](../images/docs/03-kubernetes-control-plane/06.png)

### 1.3 Load br_netfilter kernel module for Kubernetes networking

> **Note** Since we are currently using the minimal installation of Rocky Linux, the br_netfilter module needs to be loaded.

```shell
modprobe br_netfilter
echo 'br_netfilter' | sudo tee /etc/modules-load.d/k8s.conf
```

![07.png](../images/docs/03-kubernetes-control-plane/07.png)

---

## CRI: Install Docker and cri-dockerd

### 2.1 Install Docker

#### 2.1.1 Set up the repository (Domestic mirror sources are used as alternatives here.)
```shell
dnf -y install dnf-plugins-core
dnf config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/rhel/docker-ce.repo
```

![08.png](../images/docs/03-kubernetes-control-plane/08.png)

#### 2.1.2 Install the Docker packages
```shell
dnf -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

![09.png](../images/docs/03-kubernetes-control-plane/09.png)

![10.png](../images/docs/03-kubernetes-control-plane/10.png)

#### 2.1.3 Domestic mirror sources are used as alternatives here.
```shell
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://docker.1ms.run"]
}
EOF
```

![11.png](../images/docs/03-kubernetes-control-plane/11.png)

#### 2.1.4 Start Docker Engine
```shell
systemctl enable --now docker
```

![12.png](../images/docs/03-kubernetes-control-plane/12.png)

#### 2.1.5 Verify that the installation is successful by running the hello-world image
```shell
docker run hello-world
```

![13.png](../images/docs/03-kubernetes-control-plane/13.png)

### 2.2 Install cri-dockerd

#### 2.2.1 Download cri-dockerd
```shell
cd ~
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.26/cri-dockerd-0.3.26.amd64.tgz
tar -xf cri-dockerd-0.3.26.amd64.tgz
mv cri-dockerd/cri-dockerd /usr/local/bin/
```

![14.png](../images/docs/03-kubernetes-control-plane/14.png)

![15.png](../images/docs/03-kubernetes-control-plane/15.png)

#### 2.2.2 Download cri-docker service and socket
```shell
cd ~
wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.service
wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.socket
mv cri-docker.service cri-docker.socket /etc/systemd/system/
```

![16.png](../images/docs/03-kubernetes-control-plane/16.png)
![17.png](../images/docs/03-kubernetes-control-plane/17.png)


> **Note**: Here we modify cri-dockerd path and specify the pause image version: --pod-infra-container-image registry.aliyuncs.com/google_containers/pause:3.10.2

![18.png](../images/docs/03-kubernetes-control-plane/18.png)

#### 2.2.3 Enable cri-docker
```shell
systemctl daemon-reload
systemctl enable --now cri-docker.socket
```

![19.png](../images/docs/03-kubernetes-control-plane/19.png)

---

## Install kubelet, kubeadm and kubectl

```shell
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.36/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.36/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF
```
![20.png](../images/docs/03-kubernetes-control-plane/20.png)

```shell
dnf -y install kubelet kubeadm kubectl --disableexcludes=kubernetes
```

![21.png](../images/docs/03-kubernetes-control-plane/21.png)

```shell
systemctl enable --now kubelet
```

![22.png](../images/docs/03-kubernetes-control-plane/22.png)

## Creating a cluster with kubeadm

> **Note**: Below commands only running on k8s-master node.

```shell
kubeadm config images pull # Optional: Pre-download the required images in advance.
```

```shell
kubeadm init \
  --apiserver-advertise-address 192.168.47.100 \
  --pod-network-cidr=10.244.0.0/16 \
  --cri-socket unix:///var/run/cri-dockerd.sock \
  --image-repository registry.aliyuncs.com/google_containers
```

| Param                        | Description                                                                           |
|------------------------------|---------------------------------------------------------------------------------------|
| apiserver-advertise-address  | Since we have a single-node control-plane, only the IP address needs to be specified. |
| pod-network-cidr             | Flannel is selected as our CNI, so its default address is adopted.                    |
| cri-socket                   | Docker is chosen as our CRI, hence its socket file is used.                           |
| image-repository             | Domestic mirror repositories are utilized to speed up image pulling.                  |

![23.png](../images/docs/03-kubernetes-control-plane/23.png)

![24.png](../images/docs/03-kubernetes-control-plane/24.png)

---

> **Note**: Since we are logged in as root, we adopt the temporary approach using admin.conf.

```shell
export KUBECONFIG=/etc/kubernetes/admin.conf
kubectl get nodes
```

![25.png](../images/docs/03-kubernetes-control-plane/25.png)
