# Phase 1 — Virtual Machine Setup

## 1.1 Creating Virtual Machines in VirtualBox

Three virtual machines were created in VirtualBox with the following settings:

| VM Name | RAM | Disk | Network 1 | Network 2 |
|---|---|---|---|---|
| control-node | 2 GB | 20 GB (VDI, dynamic) | NAT | Internal (`ansible-net`) |
| managed-node-1 | 2 GB | 15 GB (VDI, dynamic) | NAT | Internal (`ansible-net`) |
| managed-node-2 | 2 GB | 15 GB (VDI, dynamic) | NAT | Internal (`ansible-net`) |

Each VM was created through the VirtualBox interface: Machine > New. The type was set to Linux, version Ubuntu 64-bit. A new virtual disk was created in VDI format with dynamic allocation.

**Network adapters (applied to all three VMs):**

Two network adapters were configured per VM:

- **Adapter 1** — NAT, for internet access (package installation).
- **Adapter 2** — Internal Network named `ansible-net`, for direct communication between VMs.

To configure Adapter 2: VM Settings > Network > Adapter 2 > Enable Network Adapter > Attached to: Internal Network > Name: `ansible-net`.

### Screenshots

![Control Node in VirtualBox](screenshots/phase1_vm_setup/control_node_virtualbox.png)

![Node 1 in VirtualBox](screenshots/phase1_vm_setup/node1_virtualbox.png)

![Node 2 in VirtualBox](screenshots/phase1_vm_setup/node2_virtualbox.png)

---

## 1.2 Installing Ubuntu Server

Ubuntu Server 26.04 LTS was installed on each VM using the same ISO image with the following options:

- Language: English
- Installation type: Standard Ubuntu Server (not minimized)
- Partitioning: Default
- Username: `ansible` (consistent password across all machines)
- OpenSSH Server: selected during package selection

After installation, each VM was rebooted and verified to boot correctly into the Ubuntu login prompt.
