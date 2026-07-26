# How to Setup Prerequisites

> **Note**: Unless otherwise specified, all commands shall be executed on all four nodes simultaneously.

## Prepare

### 1.1 Write hostname into /etc/hosts file

![01.png](../../images/docs/infra/03-prerequisite/01.png)

### 1.2 Install `wget` command

```shell
# Install wget utility for downloading files
dnf -y install wget
```

![02.png](../../images/docs/infra/03-prerequisite/02.png)

### 1.3 Shutdown firewall

```shell
# Check current firewall status
systemctl status firewalld

# Stop and disable firewalld service
systemctl stop firewalld
systemctl disable firewalld

# Verify firewall is disabled
systemctl status firewalld
```

![03.png](../../images/docs/infra/03-prerequisite/03.png)

---

## Environment Configuration

### 2.1 Shutdown swap

```shell
# Check current swap usage
swapon --show

# Disable swap temporarily
swapoff -a

# Permanently disable swap by commenting out swap entries in /etc/fstab
sed -i '/swap/s/^/#/' /etc/fstab

# Verify swap is disabled
swapon --show
```

![04.png](../../images/docs/infra/03-prerequisite/04.png)

### 2.2 Set SELinux to permissive mode

```shell
# Check current SELinux status
getenforce

# Set SELinux to permissive mode temporarily
setenforce 0

# Permanently set SELinux to permissive mode
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

# Verify SELinux mode
getenforce
```

![05.png](../../images/docs/infra/03-prerequisite/05.png)

### 2.3 Load br_netfilter kernel module for Kubernetes networking

> **Note**: Since we are currently using the minimal installation of Rocky Linux, the br_netfilter module needs to be loaded for Kubernetes networking to work properly.

```shell
# Load br_netfilter kernel module
modprobe br_netfilter

# Make it persistent across reboots
echo 'br_netfilter' | tee /etc/modules-load.d/k8s.conf
```

![06.png](../../images/docs/infra/03-prerequisite/06.png)