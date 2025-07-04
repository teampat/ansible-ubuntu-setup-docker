# Ansible Ubuntu Docker Setup

This Ansible project automates the setup of Ubuntu servers with Docker and system optimizations for containerized workloads.

## 📋 Overview

This playbook collection performs the following tasks:

- **Basic System Setup**: Configures timezone, locale, and essential system services
- **Docker Installation**: Installs Docker CE with compose plugin and configures user permissions
- **Linux Tuning**: Optimizes system parameters for Docker and high-performance workloads

## 🚀 Features

### Basic Setup (`basic-setup.yml`)

- Sets system timezone (default: Asia/Bangkok)
- Enables systemd-timesyncd for time synchronization
- Configures UTF-8 locale
- Updates package cache and installs essential packages

### Docker Installation (`install-docker.yml`)

- Installs Docker CE from official repository
- Installs Docker Compose plugin
- Adds specified user to docker group
- Enables and starts Docker service
- Configures Docker daemon settings

### Linux Tuning (`linux-tuning.yml`)

- Reduces swap usage (vm.swappiness = 10)
- Increases file watch limits for development tools
- Optimizes network and memory parameters
- Configures system limits for containerized workloads

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
docker_user: "your_username" # User to add to docker group
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

| Variable         | Default              | Description                   |
| ---------------- | -------------------- | ----------------------------- |
| `timezone`       | `Asia/Bangkok`       | System timezone               |
| `docker_user`    | `{{ ansible_user }}` | User to add to docker group   |
| `docker_gpg_url` | Docker official GPG  | Docker repository GPG key URL |

### Tags

Use tags to run specific tasks:

```bash
# Run only sysctl tuning tasks
ansible-playbook -i inventory.ini linux-tuning.yml --tags sysctl

# Skip Docker installation
ansible-playbook -i inventory.ini site.yml --skip-tags docker
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
