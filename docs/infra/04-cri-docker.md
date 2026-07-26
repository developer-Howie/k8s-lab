# How to Install Docker

> **Note**: Unless otherwise specified, all commands shall be executed on all three nodes simultaneously.

## Install Docker

### Set up the repository

> **Note**: Domestic mirror sources are used as alternatives here for faster downloads.

```shell
# Install dnf plugins for managing repositories
dnf -y install dnf-plugins-core

# Add Docker CE repository using Aliyun mirror
dnf config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/rhel/docker-ce.repo
```

![01.png](../../images/docs/infra/04-cri-docker/01.png)

### Install the Docker packages

```shell
# Install Docker CE, CLI, containerd, buildx, and compose plugins
dnf -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

![02.png](../../images/docs/infra/04-cri-docker/02.png)

![03.png](../../images/docs/infra/04-cri-docker/03.png)

### Configure domestic mirror sources

```shell
# Create Docker configuration directory
mkdir -p /etc/docker

# Configure registry mirrors for faster image pulling
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://docker.1ms.run"]
}
EOF
```

![04.png](../../images/docs/infra/04-cri-docker/04.png)

### Start Docker Engine

```shell
# Enable and start Docker service
systemctl enable --now docker
```

![05.png](../../images/docs/infra/04-cri-docker/05.png)

### Verify the installation

```shell
# Run hello-world container to verify Docker installation
docker run hello-world
```

![06.png](../../images/docs/infra/04-cri-docker/06.png)

## Install cri-dockerd

### Download cri-dockerd

```shell
# Download cri-dockerd binary from GitHub
cd ~
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.4.4/cri-dockerd-0.4.4.amd64.tgz
tar zxf cri-dockerd-0.4.4.amd64.tgz

# Move binary to system bin directory
mv cri-dockerd/cri-dockerd /usr/local/bin/
```

![07.png](../../images/docs/infra/04-cri-docker/07.png)

![08.png](../../images/docs/infra/04-cri-docker/08.png)

### Download cri-docker service and socket files

```shell
# Download systemd service files
cd ~
wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.service
wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.socket

# Move service files to systemd directory
mv cri-docker.service cri-docker.socket /etc/systemd/system/
```

![09.png](../../images/docs/infra/04-cri-docker/09.png)
![10.png](../../images/docs/infra/04-cri-docker/10.png)

> **Note**: Here we modify cri-dockerd path and specify the pause image version to use a domestic mirror:
> 
> --pod-infra-container-image registry.aliyuncs.com/google_containers/pause:3.10.2

![11.png](../../images/docs/infra/04-cri-docker/11.png)

### Enable cri-docker

```shell
# Reload systemd daemon and enable cri-docker socket
systemctl daemon-reload
systemctl enable --now cri-docker.socket

# Verify cri-docker socket status
systemctl status cri-docker.socket
```

![12.png](../../images/docs/infra/04-cri-docker/12.png)