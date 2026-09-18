# ovh-infra

Ansible automation for configuring an Ubuntu Linux VPS hosted on OVHcloud. It prepares the server, installs K3s, and bootstraps Argo CD for GitOps-based Kubernetes deployments.

## Repository responsibility

This repository manages the Linux host and initial Kubernetes bootstrap. Kubernetes applications and long-lived cluster configuration belong in [Taku10/k3s-platform](https://github.com/Taku10/k3s-platform).

Headlamp and monitoring are still installed by transitional Ansible roles. They will move to `k3s-platform` after their Argo CD applications and Helm values are added there.

## Ansible structure

```text
ansible/
├── group_vars/
│   └── all.yml
├── roles/
│   ├── common/
│   ├── k3s/
│   ├── helm/
│   ├── argocd/
│   ├── headlamp/
│   ├── monitoring/
│   └── verify/
└── playbook.yml
```

| Role | Responsibility |
|---|---|
| `common` | Configure the hostname and base Ubuntu packages |
| `k3s` | Install K3s and configure kubeconfig |
| `helm` | Install the Helm CLI required by transitional roles |
| `argocd` | Install Argo CD and its CLI |
| `headlamp` | Install Headlamp until GitOps manages it |
| `monitoring` | Install Prometheus and Grafana until GitOps manages them |
| `verify` | Display cluster nodes and pods |

## Run the playbook

Create `ansible/inventory.ini` locally:

```ini
[k3s]
ovh-k3s ansible_host=YOUR_OVH_VPS_IP ansible_user=ubuntu
```

Run all roles:

```bash
cd ansible
ansible-playbook -i inventory.ini playbook.yml
```

Run one part of the configuration:

```bash
ansible-playbook -i inventory.ini playbook.yml --tags k3s
ansible-playbook -i inventory.ini playbook.yml --tags monitoring
ansible-playbook -i inventory.ini playbook.yml --tags verify
```
