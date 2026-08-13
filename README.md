# Pradatta's Homelab

> A self-hosted homelab for learning, development, container orchestration, automation, media serving, monitoring, networking, and infrastructure experimentation.

![Homelab Infrastructure Diagram](/Infra-Diagram.png)

## 🏠 Overview

This repository documents my personal homelab infrastructure built around **Proxmox VE**, with a focus on practical Cloud/DevOps/SRE learning.

The environment is designed to provide:

- Virtualization with Proxmox/KVM
- Linux and Windows workloads
- Kubernetes experimentation
- Docker and Portainer workloads
- Secure remote access through Tailscale
- Network failover using dual ISP connections
- Self-hosted services
- Centralized storage and backups
- Monitoring and observability
- Automation and notifications
- Infrastructure documentation

---

## 🧱 Infrastructure

### Proxmox Host

| Component | Specification |
|---|---|
| Hypervisor | Proxmox VE |
| Host OS | Debian 12 |
| CPU | Intel Core i5 6th Gen |
| CPU Topology | 4 Cores / 4 Threads |
| RAM | 24 GB DDR4 |
| OS Storage | 500 GB NVMe |
| Data Storage | 1 TB HDD |
| Network | 1 Gbps Ethernet |

### Storage Layout

```text
500 GB NVMe
└── Proxmox OS + Virtual Machines

1 TB HDD
└── Data
    ├── Backups
    ├── Media
    ├── ISOs
    └── Homelab Data
```

---

## 🌐 Network Architecture

The homelab uses two independent ISP connections:

- **Jio Fiber** — Primary connection
- **Airtel Xtreme Fiber** — Backup connection

Both connections terminate at a:

**TP-Link ER605 Omada Gigabit VPN Router**

Responsibilities:

- Dual-WAN load balancing
- Link failover
- Stateful firewall
- Traffic management
- VLAN routing
- VPN services
- Network gateway

The LAN is distributed through a:

**TP-Link TL-SG108E 8-Port Gigabit Easy Smart Switch**

### LAN

```text
Internet
   │
   ├── Jio Fiber ───────┐
   │                    │
   └── Airtel Fiber ────┤
                        ▼
                 TP-Link ER605
                Load Balance /
                  Failover
                        │
                        ▼
              TP-Link TL-SG108E
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
       Proxmox       PCs/Laptops   IoT/TV
       Server
```

### Network Details

| Setting | Value |
|---|---|
| LAN Subnet | `192.168.10.0/24` |
| Router | TP-Link ER605 |
| Switch | TP-Link TL-SG108E |
| Primary ISP | Jio Fiber |
| Backup ISP | Airtel Xtreme Fiber |
| VPN | Tailscale |
| DNS / Access | Cloudflare DNS + local hostnames |

---

## 🔐 Remote Access

Remote access is designed around a **Tailscale VPN**.

```text
Internet
   │
   ▼
Tailscale VPN
   │
   ▼
Windows VM / Bastion Host
(Tiny11)
   │
   ├── RDP
   │
   ├── Proxmox UI
   │
   └── Internal Services
```

Remote access targets include:

- Proxmox UI — `https://proxmox.local`
- SSH access
- Internal applications
- Services exposed through subdomains
- Windows Bastion VM

Security controls:

- Tailscale encrypted access
- 2FA
- ACLs
- Firewall
- Strong passwords
- SSH keys
- Regular updates and backups

> Public exposure should be minimized. Internal management interfaces should remain reachable through the VPN wherever possible.

---

## 🖥️ Virtual Machines

The current/planned VM layout includes:

| VM | Purpose |
|---|---|
| Windows VM — Tiny11 | Bastion / remote access host |
| Ubuntu Server | Utility VM |
| Kubernetes Control Plane | Kubernetes lab |
| Kubernetes Worker 1 | Kubernetes lab |
| Kubernetes Worker 2 | Kubernetes lab |
| Windows Utility VM | Windows-based testing/utilities |

The Kubernetes cluster is intended primarily for learning and experimentation.

---

## 📦 LXC Containers

| Container | Purpose |
|---|---|
| Tailscale | VPN / remote access |
| Docker + Portainer | Container management |
| Pi-hole | DNS / ad blocking |
| Home Assistant | Home automation |
| LXC Utilities | Backup scripts and utilities |

Some services are marked as optional/planned in the infrastructure diagram.

---

## 🚀 Infrastructure & Services

| Service | Purpose |
|---|---|
| Nginx Proxy Manager | Reverse proxy / HTTPS |
| Cloudflare Tunnel | Secure service publishing |
| Plex | Media server |
| File Browser | Web-based file management |
| AdGuard Home | DNS filtering / optional |
| Portainer | Docker management |
| Home Assistant | Home automation |

### Example Service Flow

```text
User
 │
 ▼
Cloudflare
 │
 ▼
Cloudflare Tunnel
 │
 ▼
Nginx Proxy Manager
 │
 ├── Service A
 ├── Service B
 ├── Service C
 └── Service D
```

---

## 💾 Storage & Backup

Storage is split between the Proxmox NVMe and external/data storage.

### Backup Strategy

Planned backup components include:

- Proxmox Backup Server (PBS)
- VM backups
- LXC backups
- Configuration backups
- External HDD backup
- Off-site / cloud backup (optional)

The goal is to maintain recoverable copies of important infrastructure and configuration data rather than relying on a single physical disk.

---

## 📊 Monitoring & Observability

The monitoring stack includes:

- **Prometheus** — metrics collection
- **Grafana** — dashboards and visualization
- **Node Exporter** — Linux host metrics
- **Alertmanager** — alert routing

Example architecture:

```text
Proxmox / Linux / Services
          │
          ▼
    Node Exporter
          │
          ▼
      Prometheus
          │
          ├──────────► Grafana
          │
          └──────────► Alertmanager
```

---

## 🤖 Automation & Notifications

Automation is centered around:

- Home Assistant
- Telegram Bot
- SMTP / Email notifications

Possible use cases:

- Infrastructure alerts
- Service availability notifications
- Backup status
- Hardware/power events
- Home automation
- Security notifications

---

## 🔌 Common Ports

| Service | Port |
|---|---:|
| Proxmox UI | `8006` |
| SSH | `22` |
| Tailscale | `41641/UDP` |
| Nginx Proxy Manager | `81`, `443` |
| Home Assistant | `8123` |
| Portainer | `9000` |
| Grafana | `3000` |
| Prometheus | `9090` |
| Pi-hole | `53`, `80`, `443` |
| Plex | `32400` |
| File Browser | `8080` |

> Port availability can vary depending on the final deployment and reverse-proxy configuration.

---

## ⚡ Power & Availability

The homelab is designed with basic availability and graceful-shutdown measures:

- UPS for graceful shutdown
- Proxmox VM auto-start
- BIOS power restore / auto-start where supported
- Wake-on-LAN where supported
- Optional smart plug for remote power cycling
- Dual ISP connectivity

---

## 🛡️ Security Model

Security principles used by the homelab:

1. Avoid exposing management services directly to the public Internet.
2. Use Tailscale for remote administrative access.
3. Enable 2FA for critical services.
4. Use firewall rules on the router and hosts.
5. Prefer SSH keys over password authentication.
6. Keep Proxmox, VMs, containers, and applications updated.
7. Maintain regular backups.
8. Separate services where practical.
9. Use HTTPS for web applications.
10. Monitor infrastructure health and failed access attempts.

---

## 📁 Repository Structure

```text
pradatta-homelab/
├── README.md
├── docs/
│   └── homelab-infrastructure.png
├── proxmox/
│   ├── README.md
│   └── configs/
├── networking/
│   ├── README.md
│   └── configs/
├── kubernetes/
│   ├── README.md
│   ├── manifests/
│   └── helm/
├── docker/
│   ├── README.md
│   └── compose/
├── monitoring/
│   ├── prometheus/
│   └── grafana/
├── automation/
│   ├── ansible/
│   └── scripts/
└── backups/
    └── README.md
```

Only non-sensitive configuration should be committed to Git.

**Never commit:**

- Passwords
- API keys
- SSH private keys
- Tailscale auth keys
- Cloudflare tokens
- `.env` files containing secrets
- Router credentials
- Personal access tokens

Use `.gitignore` and secret-management solutions where appropriate.

---

## 🗺️ Roadmap

### Phase 1 — Core Infrastructure

- [x] Proxmox installation
- [x] Dual ISP connectivity
- [x] Router failover
- [x] Gigabit LAN
- [x] Tailscale remote access
- [x] Windows bastion VM

### Phase 2 — Virtualization

- [ ] Ubuntu utility VM
- [ ] Kubernetes control plane
- [ ] Kubernetes worker nodes
- [ ] Resource monitoring
- [ ] VM backup automation

### Phase 3 — Services

- [ ] Nginx Proxy Manager
- [ ] Cloudflare Tunnel
- [ ] Portainer
- [ ] File Browser
- [ ] Plex
- [ ] Home Assistant
- [ ] AdGuard Home

### Phase 4 — Observability

- [ ] Prometheus
- [ ] Grafana
- [ ] Node Exporter
- [ ] Alertmanager
- [ ] Telegram notifications

### Phase 5 — Infrastructure Automation

- [ ] Ansible automation
- [ ] Terraform experiments
- [ ] Automated backups
- [ ] Git-based configuration management
- [ ] Kubernetes GitOps
- [ ] Disaster recovery testing

---

## 🎯 Goals

This homelab is primarily a practical learning environment for:

- Linux Administration
- Cloud Infrastructure
- DevOps
- Site Reliability Engineering
- Kubernetes
- Containerization
- Infrastructure as Code
- Networking
- Monitoring & Observability
- Automation
- Security
- Disaster Recovery

> **Learn. Build. Automate. Secure. Document. Share.**

---

## 📸 Infrastructure Diagram

The complete architecture diagram is available at:

`docs/homelab-infrastructure.png`

---

## 👨‍💻 Author

**Pradatta Praharaj**

Cloud / DevOps / Infrastructure enthusiast focused on building practical infrastructure and automation projects.

🌐 Portfolio: `https://pradatta.tech`

---

## ⚠️ Disclaimer

This repository documents a personal homelab. IP addresses, hostnames, service names, hardware allocations, and architecture may change as the environment evolves.

Never expose credentials, private keys, tokens, or other sensitive infrastructure information in this repository.
