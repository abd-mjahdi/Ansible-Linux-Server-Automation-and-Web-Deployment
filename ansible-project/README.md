# Ansible Project Bundle

This folder contains the complete Ansible configuration to deploy a demo stack split across two managed nodes:

- **node1** (192.168.56.11): Nginx reverse proxy + Flask backend (Docker Compose)
- **node2** (192.168.56.12): MariaDB database (Docker container)

It is designed to be copied to the **control-node** (192.168.56.10) and executed from there.

## How to Use

1. Copy this folder to the control-node:

```bash
scp -r ansible-project ansible@192.168.56.10:/home/ansible/
```

2. On the control-node, install the required collection:

```bash
cd /home/ansible/ansible-project
ansible-galaxy collection install -r collections/requirements.yml
```

3. Run the deployment:

```bash
ansible-playbook -i inventory.ini playbooks/deploy_app.yml
```

4. Verify:

```bash
curl http://192.168.56.11
```
