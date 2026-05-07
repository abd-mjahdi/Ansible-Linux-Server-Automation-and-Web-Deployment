# Ansible Infrastructure Automation Project

**Module:** Administration Systemes et Reseaux  
**Authors:** Group of 2 students  
**Subject:** Project 11 — Infrastructure Automation with Ansible  

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Folder Structure](#folder-structure)
3. [Environment Requirements](#environment-requirements)
4. [Infrastructure Architecture](#infrastructure-architecture)
5. [Phase 1 — Virtual Machine Setup](#phase-1--virtual-machine-setup)
6. [Phase 2 — Network Configuration](#phase-2--network-configuration)

---

## Project Overview

This project implements infrastructure automation using Ansible across a local virtual lab. The goal is to provision and configure multiple Linux servers from a single control machine using Ansible playbooks, demonstrating key concepts of systems and network administration such as SSH-based communication, inventory management, service deployment, and automated configuration.

---

## Folder Structure

The following structure is used for the full project, including documentation assets and core configuration files.

```
ansible-project/
    README.md
    inventory.ini
    ansible.cfg
    playbooks/
        install_apache.yml
        install_nginx.yml
        create_users.yml
        deploy_app.yml
    roles/
        webserver/
            tasks/
            handlers/
            templates/
            vars/
        common/
            tasks/
            handlers/
            vars/
    docs/
        screenshots/
            phase1_vm_setup/
                control_node_virtualbox.png
                node1_virtualbox.png
                node2_virtualbox.png
            phase2_network/
                control_node_ip_a.png
                node1_ip_a.png
                node2_ip_a.png
                netplan_control_node.png
                ping_test_results.png
            phase3_ssh_ansible/
            phase4_playbooks/
            phase5_roles/
        report/
            project_report.docx
        slides/
            presentation.pptx
```

**Notes on the folder structure:**

The `docs/screenshots/` directory is organized by project phase. Each phase has its own subdirectory so that screenshots can be easily referenced in the final report and presentation. The `playbooks/` directory holds all automation scripts. The `roles/` directory contains reusable Ansible role definitions introduced in Phase 5.

---

## Environment Requirements

| Component       | Details                              |
|----------------|--------------------------------------|
| Host OS         | Windows or Linux with VirtualBox     |
| Hypervisor      | Oracle VirtualBox                    |
| Guest OS        | Ubuntu Server 26.04 LTS              |
| Ansible Version | 2.x (installed on control node only) |
| Network Mode 1  | NAT (internet access)                |
| Network Mode 2  | Internal Network named ansible-net   |

---

## Infrastructure Architecture

The lab consists of three virtual machines connected through an internal network. Ansible is installed only on the control node. It communicates with the managed nodes over SSH using the internal network interface.

```
                        NAT (Internet Access)
                               |
            +------------------+------------------+
            |                  |                  |
     [control-node]      [managed-node-1]   [managed-node-2]
     192.168.56.10        192.168.56.11      192.168.56.12
            |                  |                  |
            +------------------+------------------+
                    Internal Network: ansible-net
                      (SSH communication only)
```

| VM Name         | Hostname         | Role             | RAM   | Disk  | Internal IP   |
|----------------|-----------------|------------------|-------|-------|---------------|
| control-node    | control-node     | Ansible Control  | 2 GB  | 20 GB | 192.168.56.10 |
| managed-node1   | managed-node1    | Managed Target   | 2 GB  | 15 GB | 192.168.56.11 |
| managed-node2   | managed-node2    | Managed Target   | 2 GB  | 15 GB | 192.168.56.12 |

All three machines share the same OS user credentials for simplicity in the lab environment:

| Field    | Value      |
|----------|------------|
| Username | ansible    |
| Password | (shared)   |

---

## Phase 1 — Virtual Machine Setup

### 1.1 Creating Virtual Machines in VirtualBox

Three virtual machines were created in VirtualBox with the following individual settings.

**Control Node settings:**

Each VM was created through the VirtualBox interface using the following steps. Under Machine, New was selected. The name was set to control-node, managed-node1, or managed-node2 depending on the VM. The type was set to Linux and the version to Ubuntu 64-bit. RAM was assigned as specified in the table above and a new virtual disk was created in VDI format with dynamic allocation.

**Network adapters (applied to all three VMs):**

Two network adapters were configured per VM. Adapter 1 was set to NAT to provide internet access for package installation. Adapter 2 was set to Internal Network with the name ansible-net to allow direct communication between the virtual machines.

To configure Adapter 2: navigate to VM Settings, then Network, then Adapter 2, check Enable Network Adapter, set Attached to Internal Network, and enter ansible-net as the name.

### 1.2 Installing Ubuntu Server

Ubuntu Server 26.04 LTS was installed on each VM using the same ISO image. During installation, the following options were selected:

The language was set to English. A standard Ubuntu Server installation was chosen (not minimized). The default partitioning was accepted. The username was set to ansible with a consistent password across all machines. OpenSSH Server was selected during the package selection step to enable SSH access immediately after installation.

After installation, each VM was rebooted and verified to boot correctly into the Ubuntu login prompt.

---

## Phase 2 — Network Configuration

### 2.1 Problem Identified

After initial installation, the command `ip a` was run on each VM. It was observed that the `enp0s8` interface (corresponding to the internal ansible-net adapter) had no IP address assigned. Static IP addresses were therefore configured manually using Netplan.

### 2.2 Configuring Static IPs with Netplan

On each VM, the Netplan configuration file was edited at the following path:

```
/etc/netplan/00-installer-config.yaml
```

The existing configuration for `enp0s3` (NAT interface) was preserved and the block for `enp0s8` was added with a static address.

**Configuration applied on control-node:**

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.56.10/24
```

**Configuration applied on managed-node-1:**

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.56.11/24
```

**Configuration applied on managed-node-2:**

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.56.12/24
```

After editing each file, the configuration was applied using the following command:

```bash
sudo netplan apply
```

### 2.3 Verification

The IP assignment was confirmed on each node using:

```bash
ip a show enp0s8
```

Connectivity between all three machines was verified by running ping tests from the control node:

```bash
ping 192.168.56.11
ping 192.168.56.12
```

Both ping tests returned successful responses, confirming that the internal network is fully operational and ready for Ansible communication.

---

*Documentation will continue as the project progresses through the remaining phases.*