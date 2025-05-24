# Proxmox Homelab CI/CD & Application Hosting Setup

This document outlines the setup for a comprehensive homelab environment on a Dell PowerEdge R720 (24 Cores / 96GB RAM) running Proxmox VE. 
The goal is to establish a fully open-source CI/CD pipeline for Docker images and host various applications.

## I. Proxmox VE Host (Dell PowerEdge R720)

-   [ ] **Install Proxmox VE:** Latest stable version.
-   [ ] **Configure Storage:**
    -   [ ] **OS/Proxmox:** Fast SSDs (e.g., 2x SSDs in ZFS mirror).
    -   [ ] **VM/CT Storage:** Larger capacity SSDs (e.g., 2-4 SSDs in ZFS RAIDZ1 or RAID10).
    -   [ ] **ISO/Templates/Backups:** Larger HDDs (e.g., 2x HDDs in ZFS mirror or NAS share).
-   [ ] **Configure Networking:**
    -   [ ] Bond NICs (LACP) if switch supports.
    -   [ ] Setup VLANs (e.g., Management, VMs/CTs, DMZ).
-   [ ] **System Updates:** Ensure Proxmox VE is fully updated.
-   [ ] **Create Base Templates:**
    -   [ ] Ubuntu Server LTS VM Template.
    -   [ ] Ubuntu Server LTS CT Template.

## II. Management & Core Infrastructure VMs/CTs

-   [ ] **CT 1: Ad Blocker / DNS Server (e.g., Pi-hole/AdGuard Home)**
    -   [ ] Deploy from Ubuntu CT template.
    -   [ ] Install Pi-hole or AdGuard Home.
    -   [ ] Configure DNS settings.
    -   [ ] Point network clients to this DNS.
    -   *Resources: 1-2 vCPUs, 512MB-1GB RAM, 8GB Disk*
-   [ ] **CT 2: Reverse Proxy & SSL Management (e.g., Nginx Proxy Manager)**
    -   [ ] Deploy from Ubuntu CT template.
    -   [ ] Install Docker & Docker Compose.
    -   [ ] Deploy Nginx Proxy Manager (Docker container).
    -   [ ] Configure initial access and SSL.
    -   *Resources: 2 vCPUs, 1-2 GB RAM, 16GB Disk*

## III. CI/CD & DevOps Stack VMs/CTs

-   [ ] **VM 1: Git & CI/CD Orchestration Server (Gitea & Drone Server)**
    -   [ ] Deploy from Ubuntu VM template.
    -   [ ] Install Gitea (binary or Docker).
        -   [ ] Configure Gitea (database, admin user).
        -   [ ] Create initial repositories.
    -   [ ] Install Docker & Docker Compose.
    -   [ ] Deploy Drone CI Server (Docker container).
        -   [ ] Configure Drone Server (connect to Gitea, secrets).
    -   *Resources: 4-6 vCPUs, 8-16 GB RAM, 50-100GB Disk*
-   [ ] **VM 2 (or CTs): Drone CI Runner(s)**
    -   [ ] Deploy from Ubuntu VM/CT template(s).
    -   [ ] Install Docker Engine.
    -   [ ] Deploy Drone Docker Runner (Docker container).
        -   [ ] Register runner(s) with Drone Server.
    -   *Resources (per runner): 2-4 vCPUs, 4-8 GB RAM, 30-50GB Disk*
-   [ ] **VM 3: Artifact & Container Registry Server (Harbor)**
    -   [ ] Deploy from Ubuntu VM template.
    -   [ ] Install Docker & Docker Compose.
    -   [ ] Deploy Harbor using its `install.sh` script (which uses Docker Compose).
        -   [ ] Configure Harbor (hostname, admin password, storage).
        -   [ ] (Optional) Configure vulnerability scanning.
    -   *Resources: 4-6 vCPUs, 8-16 GB RAM, 100GB+ Disk*
    -   [ ] (Alternative) Configure Gitea's built-in container registry (if skipping dedicated Harbor).

## IV. Application Hosting / Deployment Target VMs/CTs

-   [ ] **VM 4 (or CT 3): Docker Application Host 1 ("Prod-like")**
    -   [ ] Deploy from Ubuntu VM/CT template.
    -   [ ] Install Docker Engine & Docker Compose.
    -   [ ] (Optional) Install Watchtower (pointing to Harbor).
    -   [ ] (Optional) Install Portainer.
    -   *Resources: 4-8 vCPUs, 8-16 GB RAM, 50-100GB Disk*
-   [ ] **VM 5 (or CT 4): Docker Application Host 2 ("Staging/Dev")**
    -   [ ] Deploy from Ubuntu VM/CT template.
    -   [ ] Install Docker Engine & Docker Compose.
    -   *Resources: 2-4 vCPUs, 4-8 GB RAM, 50GB Disk*
-   [ ] **(Optional) Kubernetes Cluster (e.g., K3s)**
    -   [ ] Deploy 1x Master K3s VM.
    -   [ ] Deploy 2x Worker K3s VMs.
    -   [ ] Initialize K3s cluster.
    -   [ ] (Optional) Install Argo CD / Flux CD for GitOps.

## V. Monitoring & Logging (Optional)

-   [ ] **CT 5: Monitoring Stack (Prometheus, Grafana, Loki)**
    -   [ ] Deploy from Ubuntu CT template.
    -   [ ] Install Docker & Docker Compose.
    -   [ ] Deploy Prometheus (Docker container).
    -   [ ] Deploy Grafana (Docker container).
    -   [ ] (Optional) Deploy Loki (Docker container).
    -   [ ] Configure data sources and basic dashboards.
    -   *Resources: 2-4 vCPUs, 4-8 GB RAM, 50GB Disk*

## VI. General Configuration & Best Practices

-   [ ] **Proxmox Backups:**
    -   [ ] Configure scheduled backups for all critical VMs/CTs.
    -   [ ] Verify backup storage location and retention.
-   [ ] **Security Hardening:**
    -   [ ] Strong passwords for all services and OS accounts.
    -   [ ] Regular system updates (Proxmox host & guests).
    -   [ ] Configure host firewalls (`ufw` on Ubuntu guests).
    -   [ ] Minimize exposed ports.
-   [ ] **Documentation:**
    -   [ ] Keep notes on configurations, IP addresses, credentials (securely stored).
-   [ ] **CI/CD Pipeline Setup:**
    -   [ ] Create a sample application with a Dockerfile.
    -   [ ] Push to Gitea.
    -   [ ] Create `.drone.yml` pipeline:
        -   [ ] Build step.
        -   [ ] Push to Harbor step.
        -   [ ] Deployment step (e.g., SSH + `docker-compose up`).
    -   [ ] Test the full pipeline.

## VII. Future Enhancements

-   [ ] Centralized Authentication (e.g., FreeIPA, Keycloak).
-   [ ] More advanced GitOps with ArgoCD/Flux.
-   [ ] Distributed storage (e.g., Ceph - if you have enough nodes/disks).
-   [ ] Automated SSL certificate management for internal services.
