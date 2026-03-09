# ansible-homelab

Ansible playbooks for managing a Raspberry Pi homelab cluster (pi1, pi2, pi3).

## Cluster Overview

| Host | Static IP  | Current Roles                |
|------|------------|------------------------------|
| pi1  | 10.0.0.1   | Docker, Python, Node.js, BBS |
| pi2  | 10.0.0.2   | Docker, Python               |
| pi3  | 10.0.0.3   | Docker, Python, MariaDB      |

## Prerequisites

- Ansible installed on the control machine
- SSH access to all Pi hosts with sudo privileges
- Run the setup script to pull required roles:

```bash
bash setup.sh
```

This clones [geerlingguy/ansible-role-docker](https://github.com/geerlingguy/ansible-role-docker) into `roles/`.

Install required Ansible collections:

```bash
ansible-galaxy collection install community.docker
```

## Inventory

- `inventory.yml` — main host/group definitions
- `inventory-network.yml` — network config (DHCP → static IP migration)

## Playbooks

| Playbook                  | Hosts        | Purpose                                      |
|---------------------------|--------------|----------------------------------------------|
| `playbook-network.yml`    | all          | Configure static IPs and `/etc/hosts`        |
| `playbook-docker.yml`     | all          | Install Docker                               |
| `playbook-python.yml`     | all          | Install PIP Python Package Manager           |
| `playbook-nodejs.yml`     | pi1          | Install Node.js and NPM                      |
| `playbook-mariadb.yml`    | pi3          | Deploy MariaDB in Docker                     |
| `playbook-dir.yml`        | pi3          | Create MariaDB data directory                |
| `playbook-bbs.yml`        | pi1          | Install OpenJDK 21 and Maven for Petscii BBS |
| `playbook-modbus.yml`     | —            | Modbus/RS485 tools (WIP)                     |
| `playbook-shutdown.yml`   | all          | Schedule cluster shutdown                    |

## Usage

Run a specific playbook:

```bash
ansible-playbook -i inventory.yml playbook-docker.yml
```

Network configuration uses a separate inventory:

```bash
ansible-playbook -i inventory-network.yml playbook-network.yml
```

## Notes

- The MariaDB playbook uses a default password — update it before running in production.
- `playbook-modbus.yml` is incomplete (no target hosts defined).
- Initial setup assumes hosts are reachable via DHCP on the 192.168.1.x network; after running `playbook-network.yml` they migrate to static 10.0.0.x addresses.