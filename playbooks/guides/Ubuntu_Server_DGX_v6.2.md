<h1> NVIDIA Cloud Native Core Ubuntu Server (x86-64) for Developers </h1>

This page describes the steps required to use Ansible to install the NVIDIA Cloud Native Core for Developers

NVIDIA Cloud Native Core for Developers includes:
- Ubuntu 22.04.4 LTS
- Containerd 1.6.6
- Kubernetes version 1.24.2
- Helm 3.9.0
- NVIDIA GPU Driver: 470.129.06
- NVIDIA Container Toolkit: 1.7.0
- NVIDIA GPU Operator 1.11.0
  - NVIDIA K8S Device Plugin: 0.12.2
  - NVIDIA DCGM-Exporter: 2.4.5-2.6.7
  - NVIDIA DCGM: 2.4.5-1
  - NVIDIA GPU Feature Discovery: 0.6.1
  - NVIDIA K8s MIG Manager: 0.4.1
  - NVIDIA Driver Manager: 0.4.0
  - Node Feature Discovery: 0.10.1


### The following Ansible Playbooks are available

- [Install NVIDIA Cloud Native Core](https://github.com/NVIDIA/cloud-native-core/blob/master/playbooks/cnc-docker.yaml)

- [Uninstall NVIDIA Cloud Native Core](https://github.com/NVIDIA/cloud-native-core/blob/master/playbooks/cnc-uninstall.yaml)

## Prerequisites

The following instructions assume the following:

- You have [NVIDIA DGX Systems with A100](https://docs.nvidia.com/dgx/index.html). 
- You will perform a clean install.

For more information about DGX A100 System, please refer [here](https://docs.nvidia.com/dgx/pdf/dgxa100-user-guide.pdf)

Please note that NVIDIA Cloud Native Core is validated only on systems with the default kernel (not HWE).


### Installing the DGX Operating System
These instructions require installing DGX System Operating System 5.3.1. DGX Operating System can be downloaded [here](https://docs.nvidia.com/dgx/dgx-os-5-user-guide/index.html#obtain-dgx-iso).

Please reference the [DGX System OS Installation Guide](https://docs.nvidia.com/dgx/dgx-os-5-user-guide/index.html).


## Using the Ansible playbooks 
This section describes how to use the ansible playbooks.

### Clone the git repository

Run the below commands to clone the NVIDIA Cloud Native Core ansible playbooks.

```
git clone https://github.com/NVIDIA/cloud-native-core.git
cd cloud-native-core/playbooks
```

Update the hosts file in playbooks directory with master and worker nodes(if you have) IP's with username and password like below

```
nano hosts

[master]
10.110.16.178 ansible_ssh_user=nvidia ansible_ssh_pass=nvidipass ansible_sudo_pass=nvidiapass ansible_ssh_common_args='-o StrictHostKeyChecking=no'
[node]
10.110.16.179 ansible_ssh_user=nvidia ansible_ssh_pass=nvidiapass ansible_sudo_pass=nvidiapass ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

### Installation

Install the NVIDIA Cloud Native Core stack by running the below command. "Skipping" in the ansible output refers to the Kubernetes cluster is up and running.
```
$ nano cnc_version.yaml

cnc_version: 6.2

```

```
$ nano cnc_values_6.2.yaml

# GPU Operator Values
gpu_driver_version: "515.48.07"
enable_mig: no
mig_profile: all-disabled
enable_gds: no
enable_secure_boot: no
enable_vgpu: no
vgpu_license_server: ""
## This is most likely GPU Operator Driver Registry
gpu_operator_driver_registry: "nvcr.io/nvidia"
gpu_operator_registry_username: "$oauthtoken"
## This is most likely an NGC API key
gpu_operator_registry_password: ""
## This is most likely an NGC email
gpu_operator_registry_email: ""

# Network Operator Values
## If the Network Operator is yes then make sure enable_rdma as well yes
enable_network_operator: no
## Enable RDMA yes for NVIDIA Certification
enable_rdma: no

# Prxoy Configuration
proxy: no
http_proxy: ""
https_proxy: "" 

# Cloud Native Core for DGX
cnc_dgx: yes

# Cloud Native Core for Developers Values
## Enable for Cloud Native Core Developers 
cnc_docker: no
## Enable For Cloud Native Core Developers with TRD Driver
cnc_nvidia_driver: no

## Kubernetes apt resources
k8s_apt_key: "https://packages.cloud.google.com/apt/doc/apt-key.gpg"
k8s_apt_repository: "deb https://apt.kubernetes.io/ kubernetes-xenial main"


```

`NOTE:` The host may reboot through the install. if it does, wait for the host to finish the reboot and run the installer again. 
```
bash setup.sh install
```

#### Custom Configuration
By default Cloud Native Core uses Google kubernetes apt repository, if you want to use any other kubernetes apt repository, please adjust the `k8s_apt_key` and `k8s_apt_repository` parameters from the `cnc_values_6.2.yaml` file

Example:
```
## Kubernetes apt resources
k8s_apt_key: "https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg"
k8s_apt_repository: "deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main"
```

### Uninstall

Run the below command to uninstall the NVIDIA Cloud Native Core. Tasks being "ignored" refers to no kubernetes cluster being available.

```
bash setup.sh uninstall
```

