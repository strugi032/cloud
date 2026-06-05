# Kubernetes Deployment with Kubespray

## Table of Contents

*   [Purpose](#purpose)
*   [What Is Kubespray?](#what-is-kubespray)
*   [When to Use Kubespray](#when-to-use-kubespray)
*   [Repository Structure](#repository-structure)
*   [High-Level Flow](#high-level-flow)
*   [Prerequisites](#prerequisites)
*   [Node Sizing and Requirements](#node-sizing-and-requirements)
*   [Inventory Structure](#inventory-structure)
*   [Cluster Configuration](#cluster-configuration)
*   [Deploying the Cluster](#deploying-the-cluster)
*   [Post-Deployment Validation](#post-deployment-validation)
*   [Scaling the Cluster](#scaling-the-cluster)
*   [Node Replacement](#node-replacement)
*   [Node Provisioning & OS Baseline](#node-provisioning--os-baseline)
*   [Upgrading the Cluster](#upgrading-the-cluster)
*   [Backup and Restore: etcd](#backup-and-restore-etcd)
*   [General Kubernetes Backup](#general-kubernetes-backup)
*   [Maintenance: Draining and Patching](#maintenance-draining-and-patching)
*   [Resetting the Cluster](#resetting-the-cluster)
*   [Common Issues](#common-issues)
*   [Best Practices](#best-practices)
*   [Definition of Done](#definition-of-done)
*   [Summary](#summary)

---

## Purpose

This document describes how to deploy and manage a self-managed Kubernetes cluster using **Kubespray** and **Ansible**. It provides a practical engineering runbook for provisioning clusters in environments where managed services are not available or where fine-grained control over the control plane is required.

It is particularly useful for:
*   Virtual Machine (VM) environments
*   Bare metal deployments
*   Private cloud infrastructure
*   On-premises data centers
*   Lab or development environments

> [!NOTE]
> This document is not a replacement for the official Kubespray documentation. It is a practical engineering runbook for understanding the deployment flow, required inputs, validation steps, and operational risks.

---

## What Is Kubespray?

Kubespray is an Ansible-based project used to deploy and manage production-ready Kubernetes clusters. It leverages `kubeadm` under the hood but automates the heavy lifting of environment preparation and component configuration.

Kubespray automates:
*   Operating system preparation (packages, sysctl, kernel modules)
*   Container runtime installation (containerd, Docker)
*   Kubernetes control plane setup
*   Worker node provisioning
*   Highly available etcd cluster setup
*   CNI installation (Calico, Flannel, Cilium, etc.)
*   Kubelet and kubeadm configuration

> [!IMPORTANT]
> Kubespray creates a self-managed Kubernetes cluster. The team is responsible for upgrades, monitoring, backups, security, and operational support of both the workloads and the control plane itself.

---

## When to Use Kubespray

### Recommended Use Cases
*   **On-prem / Bare Metal:** When you need to run Kubernetes directly on physical hardware.
*   **Private Cloud:** Deploying on OpenStack, vSphere, or other private virtualization layers.
*   **Restricted Environments:** Air-gapped or highly regulated environments where external access is limited.
*   **Deep Customization:** When you need specific CNI, storage, or runtime configurations not offered by managed services.
*   **Learning:** Excellent for understanding how Kubernetes components are wired together.

### When Not to Use Kubespray
*   **Public Cloud (EKS/AKS/GKE):** If a managed service is available, it is generally preferred to reduce operational overhead.
*   **Limited Ops Capacity:** If the team cannot commit to regular control plane maintenance and incident response.
*   **Serverless Preference:** When the goal is to avoid managing infrastructure entirely.

---

## Repository Structure

The following structure is recommended for managing Kubespray-based automation within this project:

```text
ansible/
├── README.md
├── docs/
│   └── kubespray-kubernetes-deployment.md
├── inventories/
│   └── lab/
│       ├── inventory.ini
│       └── group_vars/
│           ├── all/
│           │   └── all.yml
│           └── k8s_cluster/
│               └── k8s-cluster.yml
├── scripts/
│   ├── check-ssh.sh
│   ├── validate-nodes.sh
│   └── run-kubespray.sh
└── examples/
    └── inventory.ini.example
```

---

## High-Level Flow

The deployment process follows this linear progression:

```text
Prepare Nodes -> Configure SSH -> Create Inventory -> Configure Cluster -> Run Kubespray -> Validate Cluster
```

---

## Prerequisites

Before starting the deployment, ensure the following are available on your management machine:

*   **Git:** To clone the Kubespray repository.
*   **Python 3.x:** Required for Ansible.
*   **venv:** Python virtual environment for dependency isolation.
*   **SSH Access:** Public key authentication to all target nodes.
*   **Sudo Access:** The user must have passwordless sudo or a known sudo password.
*   **Ansible:** The version must match the requirements of your selected Kubespray release.

### Initial Setup

```bash
# Clone Kubespray
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray

# Create and activate virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

> [!WARNING]
> Always check Kubespray version compatibility before deployment. Supported Kubernetes versions, Ansible versions, operating systems, and container runtimes can change between releases.

---

## Node Sizing and Requirements

All target nodes must meet baseline criteria for a stable cluster. The following recommendations are baseline starting points and should be adjusted based on real-world load.

### 1. Hardware Requirements Table

| Environment | Node Role | Node Count | CPU per Node | RAM per Node | Disk per Node | Notes |
| :--- | :--- | :---: | :---: | :---: | :---: | :--- |
| **Test/Lab** | Combined control-plane + worker | 1-3 | 2-4 vCPU | 4-8 GB | 40-80 GB | Suitable for learning, testing Kubespray, validating configs |
| **Test/Lab** | Worker | 1-2 | 2-4 vCPU | 4-8 GB | 40-80 GB | Optional, useful if testing scheduling and node separation |
| **Production** | Control Plane | 3 | 4 vCPU | 8-16 GB | 80-120 GB | Required for HA control plane |
| **Production** | Worker | 3+ | 4-8 vCPU | 16-32 GB | 100+ GB | Scale based on application workloads |
| **Production** | Dedicated etcd | 3 | 2-4 vCPU | 8-16 GB | 80-120 GB fast disk | Optional but recommended for larger or stricter HA setups |

### 2. Sizing Considerations
*   **etcd Placement:** For small production clusters, control-plane nodes can also run etcd. For larger or more critical environments, use dedicated etcd nodes to isolate I/O.
*   **Workload-Based Sizing:** Worker node sizing should be based on real workload resource requests, limits, and expected traffic.
*   **System Overhead:** Monitoring, logging, ingress controllers, service mesh, and storage systems (e.g., Ceph) can significantly increase resource requirements.
*   **Validation:** Always validate sizing with capacity testing before production go-live.

### 3. Sizing Factors & Disclaimer

> [!WARNING]
> **Disclaimer:** These specifications are general guidelines. There is no "one size fits all" configuration for Kubernetes.

Proper sizing depends on:
*   Number of workloads (Pods)
*   Resource requests and limits
*   Expected network traffic and throughput
*   Storage performance needs (IOPS)
*   Monitoring and logging stack requirements
*   High Availability (HA) and disaster recovery goals

---

## Inventory Structure

Kubespray uses a standard Ansible inventory. It is recommended to keep your inventory files in a separate directory from the Kubespray source code.

### Lab Inventory Example (`inventory.ini`)

```ini
[all]
node1 ansible_host=192.168.56.11 ip=192.168.56.11
node2 ansible_host=192.168.56.12 ip=192.168.56.12
node3 ansible_host=192.168.56.13 ip=192.168.56.13

[kube_control_plane]
node1

[etcd]
node1

[kube_node]
node2
node3

[k8s_cluster:children]
kube_control_plane
kube_node
```

### Production HA Example
For high availability, use 3 nodes for the control plane and etcd roles, with dedicated worker nodes.

---

## Cluster Configuration

Kubespray configuration is managed via `group_vars`. The two most important files are:

1.  `inventory/<cluster-name>/group_vars/all/all.yml` (Global settings)
2.  `inventory/<cluster-name>/group_vars/k8s_cluster/k8s-cluster.yml` (Kubernetes specific)

### Common Settings

```yaml
# Use containerd as the runtime
container_manager: containerd

# Select CNI plugin
kube_network_plugin: calico

# Kube-proxy mode
kube_proxy_mode: ipvs

# Cluster Name
cluster_name: cluster.local
```

> [!WARNING]
> Pod CIDR and Service CIDR should be planned before cluster creation. Changing them later is a significant operational task that can cause network downtime.

---

## Deploying the Cluster

### 1. Validate Connectivity
Ensure Ansible can communicate with all nodes and has sudo privileges.

```bash
ansible -i inventory/lab/inventory.ini all -m ping -b
```

### 2. Execute Playbook
Run the main cluster deployment playbook.

```bash
ansible-playbook -i inventory/lab/inventory.ini cluster.yml -b -v
```

If using a specific private key:
```bash
ansible-playbook -i inventory/lab/inventory.ini cluster.yml -b -v --private-key ~/.ssh/id_rsa
```

---

## Post-Deployment Validation

Once the playbook finishes, verify the cluster state.

### Node and Pod Status
```bash
# Check node readiness
kubectl get nodes -o wide

# Check system component health
kubectl get pods -A
```

### Network and DNS Test
```bash
# Run a DNS lookup test
kubectl run dns-test --image=busybox:1.36 --rm -it --restart=Never -- nslookup kubernetes.default
```

### Test Workload
```bash
kubectl create deployment nginx-test --image=nginx
kubectl expose deployment nginx-test --port=80 --type=ClusterIP
kubectl get pods,svc

# Cleanup
kubectl delete deployment nginx-test
kubectl delete service nginx-test
```

---

## Scaling the Cluster

To add a new worker node to an existing cluster:
1.  Provision the new VM/hardware.
2.  Ensure SSH access and sudo are configured.
3.  Add the new node to the `[all]` and `[kube_node]` sections of your `inventory.ini`.
4.  Run the scale playbook:

```bash
# Scale the cluster by adding new nodes defined in the inventory
ansible-playbook -i inventory/lab/inventory.ini scale.yml -b -v
```

---

## Node Replacement

If a node fails or needs to be replaced due to aging hardware:

### 1. Graceful Removal
If the node is still reachable, first drain it and remove it from the cluster:

```bash
# Drain the node of all workloads
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data

# Remove the node from the Kubernetes API
kubectl delete node <node-name>

# Use Kubespray to clean up the node and remove it from the configuration
ansible-playbook -i inventory/lab/inventory.ini remove-node.yml -b -v -e "node=<node-name>"
```

### 2. Physical Replacement
1.  Update your `inventory.ini` with the new node's details (keep the same name if preferred, or use a new one).
2.  If the name changed, remove the old entry.

### 3. Provision New Node
Follow the **Scaling the Cluster** steps to add the new node back into the rotation:

```bash
ansible-playbook -i inventory/lab/inventory.ini scale.yml -b -v
```

---

## Node Provisioning & OS Baseline

Before a node can be added to the inventory and processed by Kubespray, it must have a base OS installed and fundamental configurations applied.

### 1. Provisioning Options
*   **Virtualization Tools:** Use Terraform (vSphere/KVM), Packer (Golden Images), or Cloud-Init for initial VM creation and OS installation.
*   **Bare Metal:** Use PXE/iPXE, MAAS (Metal as a Service), or manual ISO installation.

### 2. OS Baseline via Ansible
It is highly recommended to run a "Baseline" Ansible playbook on new nodes before Kubespray. This should ensure:
*   **SSH:** Public keys are authorized; passwordless sudo is configured.
*   **Python:** Python 3 is installed.
*   **Network:** Static IP or DHCP reservation is set.
*   **Disk:** Partitions (especially `/var/lib/docker` or `/var/lib/containerd`) are correctly sized.
*   **Security:** Firewall is disabled or configured with [Kubernetes required ports](https://kubernetes.io/docs/reference/ports-and-protocols/).

---

## Upgrading the Cluster

Upgrades should be performed incrementally (e.g., 1.25.x to 1.26.x). 

### 1. Preparation
1.  Review [Kubespray Release Notes](https://github.com/kubernetes-sigs/kubespray/releases).
2.  Update your local Kubespray repository to the desired version tag.
3.  Backup critical data, especially the etcd database.

### 2. Configure Target Version
In `inventory/<cluster-name>/group_vars/k8s_cluster/k8s-cluster.yml`, update the version:

```yaml
kube_version: v1.26.3
```

### 3. Run Upgrade Playbook
This playbook performs a rolling upgrade of all components (etcd, control plane, and workers).

```bash
# Perform a rolling upgrade of the entire cluster
ansible-playbook -i inventory/lab/inventory.ini upgrade-cluster.yml -b -v
```

> [!WARNING]
> Kubernetes upgrades are operationally sensitive. Do not upgrade production clusters without a verified backup and a tested rollback/recovery strategy.

---

## Backup and Restore: etcd

The etcd database stores the entire state of the Kubernetes cluster. Losing etcd means losing the cluster configuration.

### 1. Automated Backup via Kubespray
Kubespray includes a playbook to perform etcd snapshots:

```bash
# Run the etcd backup playbook
ansible-playbook -i inventory/lab/inventory.ini extra_playbooks/upgrade-cluster.yml --tags=etcd_snapshot
```

### 2. Manual Snapshot (etcdctl)
Run this on an etcd node:

```bash
# Snapshot the current etcd state
ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/ssl/etcd/ca.pem \
  --cert=/etc/kubernetes/ssl/etcd/admin-node1.pem \
  --key=/etc/kubernetes/ssl/etcd/admin-node1-key.pem \
  snapshot save /tmp/etcd-snapshot.db
```

### 3. Restore Strategy
Restoring etcd is a high-risk operation that usually requires stopping all `kube-apiserver` instances.
*   Use the `etcdctl snapshot restore` command.
*   Update the etcd data directory on all control plane nodes.
*   Restart the etcd and Kubernetes services.

---

## General Kubernetes Backup

While etcd backups are for the "entire cluster," you should also backup application-level resources to ensure recovery if a version upgrade corrupts specific workloads.

### 1. Resource Export
As a baseline, export all namespace resources periodically:

```bash
# Export all resources in a namespace to YAML
kubectl get all,configmaps,secrets,ingresses -n <namespace> -o yaml > namespace-backup.yaml
```

### 2. Velero (Recommended)
For production clusters, use [Velero](https://velero.io/).
*   **Purpose:** Backs up Kubernetes objects (API) and Persistent Volumes (CSI snapshots).
*   **Upgrade Safety:** Before running `upgrade-cluster.yml`, perform a full Velero backup to an external S3-compatible store.
*   **Recovery:** If the upgrade fails catastrophically, you can rebuild the cluster from scratch and use Velero to restore all namespaces and data.

---

## Maintenance: Draining and Patching

For routine maintenance like OS kernel updates or security patching:

### 1. Drain the Node
Prevent new pods from being scheduled and evict existing ones.

```bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

### 2. Perform Maintenance
Log into the node and perform your maintenance tasks:

```bash
ssh <node-ip>
sudo apt update && sudo apt upgrade -y
sudo reboot
```

### 3. Uncordon the Node
Once the node is back online and verified, allow it to accept workloads again.

```bash
kubectl uncordon <node-name>
```

---

## Resetting the Cluster

If you need to tear down the cluster entirely:

```bash
ansible-playbook -i inventory/lab/inventory.ini reset.yml -b -v
```

> [!WARNING]
> The reset command is destructive. It will remove all Kubernetes components and data from the nodes.

---

## Common Issues

| Problem | Possible Cause | What to Check |
| :--- | :--- | :--- |
| Ansible cannot connect | SSH issue, wrong key, wrong user | SSH config, inventory, firewall rules |
| Preflight failures | Missing packages, sysctl settings | OS requirements, kernel modules |
| Image pull timeout | No internet or registry access | Proxy settings, firewall, registry mirrors |
| Pods stuck Pending | No resources or broken CNI | Node capacity, CNI logs, kubectl events |
| Node NotReady | Kubelet or CNI initialization failure | Kubelet logs, CNI pods |
| API Unreachable | Load balancer or cert issue | API server logs, firewall, cert expiry |

---

## Best Practices

*   **Version Pinning:** Always pin your Kubespray version to a specific git tag or release.
*   **Infrastructure as Code:** Keep your inventory and custom `group_vars` in version control.
*   **Stable IPs:** Use static IPs for all nodes to prevent cluster breakage after reboot.
*   **Dedicated etcd:** For production, consider dedicated nodes for etcd to isolate disk I/O.
*   **Monitoring:** Deploy Prometheus/Grafana immediately after validation.
*   **Staging Validation:** Test all upgrades and configuration changes in a lab/staging cluster first.

---

## Definition of Done

A Kubespray deployment is considered complete when:
*   [ ] All nodes are in the `Ready` state.
*   [ ] All `kube-system` pods are `Running`.
*   [ ] Internal DNS resolution is verified.
*   [ ] Pod-to-Pod and Pod-to-Service networking is functional.
*   [ ] `kubectl` access is configured for the management team.
*   [ ] Backup and restore procedures for etcd are documented and tested.
*   [ ] Operational ownership and support model are clearly defined.

---

## Summary

Kubespray provides a powerful and flexible way to manage Kubernetes clusters outside of public cloud environments. While it offers deep control, it also requires a commitment to infrastructure management and regular maintenance. By following this runbook, teams can ensure a consistent and reliable deployment lifecycle.
