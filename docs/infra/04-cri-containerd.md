# How to Install Containerd

> **Note**: Unless otherwise specified, all commands shall be executed on all three nodes simultaneously.

## Configure Kernel Parameters

### Enable Kernel Network Forwarding and Bridge Module

```shell
# Configure kernel parameters for Kubernetes networking
cat <<EOF | tee /etc/sysctl.d/k8s-network.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```

![01.png](../../images/docs/infra/04-cri-containerd/01.png)

### Reload sysctl parameters

```shell
# Reload sysctl parameters to apply changes
sysctl --system
```

![02.png](../../images/docs/infra/04-cri-containerd/02.png)

## Install Containerd

### Set up the repository

> **Note**: Domestic mirror sources are used as alternatives here for faster downloads.

```shell
# Install dnf plugins for managing repositories
dnf -y install dnf-plugins-core

# Add Docker CE repository using Aliyun mirror
dnf config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/rhel/docker-ce.repo
```

![03.png](../../images/docs/infra/04-cri-containerd/03.png)

### Install the Containerd packages

```shell
# Install containerd.io package
dnf -y install containerd.io
```

![04.png](../../images/docs/infra/04-cri-containerd/04.png)

## Configure Containerd

### Initialize Default Containerd Configuration

```shell
# Check existing containerd configuration directory
ls -l /etc/containerd

# Backup existing config file
mv /etc/containerd/config.toml /etc/containerd/config.toml_bak

# Generate default containerd configuration
containerd config default > /etc/containerd/config.toml

# Verify configuration file was created
ls -l /etc/containerd
```

![05.png](../../images/docs/infra/04-cri-containerd/05.png)

### Configure Domestic Image Acceleration Mirror

> **Note**: Configure registry mirror for faster image pulling from Docker Hub.

![06.png](../../images/docs/infra/04-cri-containerd/06.png)

![07.png](../../images/docs/infra/04-cri-containerd/07.png)

```shell
# Create certs.d directory for Docker registry configuration
mkdir -p /etc/containerd/certs.d/docker.io

# Configure host mirror for Docker Hub
tee /etc/containerd/certs.d/docker.io/hosts.toml <<-'EOF'
[host."https://docker.1ms.run"]
  capabilities = ["pull", "resolve"]
EOF
```

![08.png](../../images/docs/infra/04-cri-containerd/08.png)

## Enable Containerd Service

```shell
# Reload systemd daemon and enable containerd
systemctl daemon-reload
systemctl enable --now containerd

# Verify containerd service status
systemctl status containerd
```

![09.png](../../images/docs/infra/04-cri-containerd/09.png)

```shell
# Verify containerd CLI is working
ctr
```

![10.png](../../images/docs/infra/04-cri-containerd/10.png)

## Install CRI-Tools (crictl)

### Download and Install crictl

```shell
# Download crictl binary from GitHub
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.36.0/crictl-v1.36.0-linux-amd64.tar.gz
tar zxf crictl-v1.36.0-linux-amd64.tar.gz
```

![11.png](../../images/docs/infra/04-cri-containerd/11.png)

![12.png](../../images/docs/infra/04-cri-containerd/12.png)

![13.png](../../images/docs/infra/04-cri-containerd/13.png)

---

## Configure crictl

> **Note**: Configure crictl to connect to containerd socket and fix warning messages.

```shell
# Configure crictl to use containerd runtime
tee > /etc/crictl.yaml <<-'EOF'
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 10
debug: false
EOF
```

![14.png](../../images/docs/infra/04-cri-containerd/14.png)