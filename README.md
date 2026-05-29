# azure-monitoring-grafana

Azure infrastructure monitoring stack — Grafana connected to Azure Monitor via Managed Identity, deployed on Ubuntu Server.

![Grafana](https://img.shields.io/badge/Grafana-Monitoring-F46800?style=flat&logo=grafana)
![Azure](https://img.shields.io/badge/Azure-Monitor-0078D4?style=flat&logo=microsoftazure)
![Ubuntu](https://img.shields.io/badge/Ubuntu-Server-E95420?style=flat&logo=ubuntu)

## Overview

Deploys Grafana on an Ubuntu VM and connects it to Azure Monitor using Managed Identity authentication — no credentials stored, no service principal required. Dashboards visualize CPU, memory, and disk I/O metrics collected via Azure Monitor Agent.

## Stack

| Component | Details |
|---|---|
| Grafana | Installed via APT on Ubuntu 18.04+, running on port 3000 |
| Azure Monitor | Data source for VM performance metrics |
| Managed Identity | Passwordless authentication between Grafana and Azure |
| Azure Monitor Agent | Guest metrics collection on the target VM |
| RBAC | Reader + Monitoring Reader roles on the VM |

## Setup

### 1. Prepare Ubuntu Server

    sudo apt update && sudo apt upgrade -y

### 2. Install Grafana

    sudo apt install -y grafana
    sudo systemctl enable --now grafana-server
    sudo ufw allow 3000/tcp

### 3. Configure Managed Identity

Enable Managed Identity on the Azure VM, then assign roles:
- Reader
- Monitoring Reader

Add to /etc/grafana/grafana.ini:

    [auth.azure_auth]
    enabled = true
    managed_identity_enabled = true

    [azure]
    managed_identity_enabled = true

### 4. Connect Azure Monitor Data Source

In Grafana UI: Configuration > Data Sources > Azure Monitor
Select Managed Identity as authentication method.

### 5. Create Dashboards

Add panels for:
- CPU usage metrics
- Memory utilization
- Disk I/O performance

## Screenshots

![Grafana Dashboard](./screenshots/9.png)
![Azure Monitor Integration](./screenshots/8.png)
![CPU Metrics Panel](./screenshots/10.png)

## Troubleshooting

**Managed Identity not showing in Grafana auth options**
Manually add managed_identity_enabled = true to grafana.ini and restart the service.

**Metrics not appearing in dashboards**
Install Azure Monitor Agent on the VM and enable guest metrics via Azure Portal. Verify Monitoring Reader role is assigned.

**VM size restrictions on creation**
Use an existing VM in the subscription or request quota increase for the required size.
