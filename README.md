
# Ansible Playbook: Install Nginx on VMware VM

This repository contains an Ansible playbook that automates the installation and configuration of Nginx on a Linux-based virtual machine (VM) running in VMware Workstation or VMware Player on a Windows laptop.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
  - [1. Set Up Your Environment](#1-set-up-your-environment)
  - [2. Configure Your Inventory File](#2-configure-your-inventory-file)
  - [3. Create the Ansible Playbook](#3-create-the-ansible-playbook)
  - [4. Run the Playbook](#4-run-the-playbook)
- [Verifying the Installation](#verifying-the-installation)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Prerequisites

Before you start, make sure you have the following installed on your Windows laptop:

1. **VMware Workstation/Player**: To run the VM.
2. **A Linux-based VM**: Running Ubuntu, Debian, CentOS, or a similar distribution.
3. **Ansible**: Installed on your local machine (Windows laptop or within the VM). You can use Windows Subsystem for Linux (WSL) to run Ansible if you're on Windows.

### Additional Requirements

- **SSH Access**: Ensure that you can SSH into your VM from your Windows laptop.
- **Sudo Access**: The user running the Ansible playbook must have sudo privileges on the VM.

## Setup Instructions

### 1. Set Up Your Environment

- **Install VMware Workstation/Player**: Follow the installation instructions from the official VMware website.
- **Create or Import a VM**: Set up a Linux-based VM in VMware Workstation/Player.
- **Set Up SSH Access**: Make sure you can SSH into your VM using a tool like PuTTY or the command line.

### 2. Configure Your Inventory File

Create an inventory file that lists your VM's IP address and SSH credentials. Example `inventory.ini`:

```ini
[webserver]
192.168.23.241 ansible_user=your_vm_username ansible_ssh_pass=your_vm_password ansible_become=yes ansible_become_password=your_sudo_password
```

Replace the placeholders with your VM's IP address, SSH username, and passwords.

### 3. Create the Ansible Playbook

Create a file called `install_nginx.yml` with the following content:

```yaml
---
- name: Install and start Nginx on the VM
  hosts: webserver
  become: yes
  tasks:
    - name: Update apt package manager (for Ubuntu/Debian)
      apt:
        update_cache: yes
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Install Nginx (for Ubuntu/Debian)
      apt:
        name: nginx
        state: present
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Install Nginx (for CentOS/RHEL)
      yum:
        name: nginx
        state: present
      when: ansible_distribution in ['CentOS', 'RedHat']

    - name: Ensure Nginx is running
      service:
        name: nginx
        state: started
```

### 4. Run the Playbook

Run the Ansible playbook to install Nginx on your VM:

```bash
ansible-playbook -i inventory.ini install_nginx.yml
```

Ansible will connect to your VM, install Nginx, and ensure the service is running.

## Verifying the Installation

After running the playbook, verify that Nginx is installed and running:

1. **Check Nginx Status**:
   - SSH into your VM and run: \`systemctl status nginx\`.
   - You should see that Nginx is active and running.

2. **Access Nginx in Your Browser**:
   - Open a web browser on your Windows laptop.
   - Navigate to \`http://192.168.23.241\` (replace with your VM's IP).
   - You should see the Nginx default welcome page.

## Troubleshooting

- **SSH Issues**: Ensure that your VM is reachable over SSH and that the correct credentials are provided.
- **Sudo Privileges**: Make sure the Ansible user has the necessary sudo privileges on the VM.
- **Firewall**: If you can't access Nginx from your browser, check if there's a firewall blocking HTTP traffic on port 80.

## Contributing

If you encounter any issues or have suggestions for improvements, feel free to open an issue or submit a pull request. Contributions are always welcome!

## License

This project is licensed under the MIT License. See the `LICENSE` file for more details.

