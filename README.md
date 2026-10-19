# Ansible Ubuntu Docker Setup

This Ansible project automates the setup of Ubuntu servers with Docker and system optimizations for containerized workloads.

## 📋 Overview

This playbook collection performs the following tasks:

- **Basic System Setup**: Configures timezone, time sync, locale, and unattended upgrades
- **Docker Installation**: Installs Docker CE with compose plugin and configures user permissions
- **Linux Tuning**: Sets swap, inotify, journal, and login ulimits for container hosts

## 🚀 Features

### Basic Setup (`basic-setup.yml`)

- Sets system timezone (default: Asia/Bangkok)
- Enables chrony when it is installed; otherwise installs and enables systemd-timesyncd
- Configures UTF-8 locale
- Installs and enables unattended-upgrades

### Docker Installation (`install-docker.yml`)

- Installs Docker CE from the official repository
- Falls back to an older apt suite (default: `noble`) when this Ubuntu codename is not published yet
- Removes conflicting Ubuntu Docker packages before install
- Installs the Docker Compose plugin
- Adds the SSH user (`ansible_user`) to the docker group
- Enables and starts the Docker service
- Configures Docker daemon log rotation

### Linux Tuning (`linux-tuning.yml`)

- Reduces swap usage (`vm.swappiness = 10`)
- Increases inotify watch limits
- Limits systemd journal size
- Raises `nofile` and `nproc` for login sessions

## 📁 Project Structure

```
.
├── site.yml                 # Main playbook that imports all others
├── basic-setup.yml          # Basic system configuration
├── install-docker.yml       # Docker installation and setup
├── linux-tuning.yml         # System performance tuning
├── inventory.ini.example    # Example inventory file
├── inventory.ini            # Your inventory file (create from example)
└── README.md               # This file
```

## ⚙️ Prerequisites

- **Ansible**: Version 2.9 or higher
- **Target Servers**: Ubuntu 18.04 LTS or higher
- **SSH Access**: Passwordless SSH access to target servers
- **Sudo Privileges**: User must have sudo access on target servers

## 🛠️ Setup

### 1. Clone or Download

Download this playbook collection to your Ansible control machine.

### 2. Configure Inventory

Copy the example inventory file and customize it:

```bash
cp inventory.ini.example inventory.ini
```

Edit `inventory.ini` with your server details:

```ini
[dev]
dev-server-ip ansible_user=your_username

[staging]
staging-server-ip ansible_user=your_username
```

### 3. Customize Variables (Optional)

You can override default variables by editing the playbook files or creating a `group_vars` directory:

```yaml
# Example variables you can customize
timezone: "Asia/Bangkok" # Set your timezone
docker_repo_fallback_codename: "noble" # Docker apt suite if this release is not published yet
```

## 🚀 Usage

### Run All Playbooks

Execute the complete setup on all Ubuntu hosts:

```bash
ansible-playbook -i inventory.ini site.yml
```

### Run Individual Playbooks

You can run specific parts of the setup:

```bash
# Basic system setup only
ansible-playbook -i inventory.ini basic-setup.yml

# Docker installation only
ansible-playbook -i inventory.ini install-docker.yml

# System tuning only
ansible-playbook -i inventory.ini linux-tuning.yml

# Basic setup and tuning, skip Docker
ansible-playbook -i inventory.ini site.yml --tags basic,tuning
```

### Target Specific Host Groups

Run on specific environments:

```bash
# Run on development servers only
ansible-playbook -i inventory.ini site.yml --limit dev

# Run on staging servers only
ansible-playbook -i inventory.ini site.yml --limit staging
```

### Dry Run (Check Mode)

Test the playbook without making changes:

```bash
ansible-playbook -i inventory.ini site.yml --check
```

## 🔧 Customization

### Variables

Key variables you can customize:

| Variable                       | Default             | Description                                              |
| ------------------------------ | ------------------- | -------------------------------------------------------- |
| `timezone`                     | `Asia/Bangkok`      | System timezone                                          |
| `docker_repo_fallback_codename`| `noble`             | Docker apt suite used when the host codename is missing |
| `docker_gpg_url`               | Docker official GPG | Docker repository GPG key URL                            |

### Tags

`site.yml` tags each imported playbook: `basic`, `docker`, and `tuning`. Inside `linux-tuning.yml` you can narrow further:

```bash
# Run only sysctl tuning tasks
ansible-playbook -i inventory.ini linux-tuning.yml --tags sysctl

# Journal size or login ulimits only
ansible-playbook -i inventory.ini linux-tuning.yml --tags journald
ansible-playbook -i inventory.ini linux-tuning.yml --tags ulimit
```

## ✅ Verification

After running the playbooks, verify the installation:

```bash
# Check Docker installation
docker --version
docker compose version

# Verify Docker service is running
sudo systemctl status docker

# Test Docker without sudo (may require re-login)
docker run hello-world

# Check system tuning
sysctl vm.swappiness
sysctl fs.inotify.max_user_watches
```

## 🔍 Troubleshooting

### Common Issues

1. **Permission Denied for Docker**: Log out and back in after running the playbook to refresh group membership.

2. **SSH Connection Issues**: Ensure your SSH key is added to the target server and the user has sudo privileges.

3. **Package Installation Fails**: Check internet connectivity and ensure the target server can reach package repositories.

### Debug Mode

Run with verbose output for troubleshooting:

```bash
ansible-playbook -i inventory.ini site.yml -vvv
```

## 📝 Notes

- The playbooks are idempotent - safe to run multiple times
- All tasks require sudo privileges on the target servers
- Docker installation uses the official Docker repository for latest stable versions
- System tuning parameters are optimized for containerized workloads

## 🤝 Contributing

Feel free to customize these playbooks for your specific needs. Consider:

- Adding additional system packages
- Customizing Docker daemon configuration
- Adding security hardening tasks
- Including monitoring tools setup

## 📄 License

This project is provided as-is for educational and operational use.
