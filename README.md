# Ansible DevOps Practices (Under Development)

## Project Overview

This project is part of the DataScientest bootcamp, focusing on practicing and mastering Ansible automation techniques in a DevOps context. The purpose of this project is to develop hands-on experience with Ansible by setting up a control node to manage two target nodes, automating the setup and configuration of web services, and experimenting with AWX for orchestrating complex automation workflows. This README documents the step-by-step process, tools used, and technical details for each component.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Project Goals](#project-goals)
3. [Technologies Used](#technologies-used)
4. [Prerequisites](#prerequisites)
5. [Setup Guide](#setup-guide)
6. [Ansible Playbooks Overview](#ansible-playbooks-overview)
7. [AWX Integration](#awx-integration)
8. [Lessons Learned and Reflections](#lessons-learned-and-reflections)

---

## Introduction

Ansible is an open-source automation tool used to manage and configure systems. This project demonstrates core DevOps practices using Ansible and Docker to simulate a multi-host environment. By setting up and managing a control node and multiple target nodes, this project provides hands-on practice with automating infrastructure tasks such as user creation, SSH access management, service management, and more.

This project also incorporates **AWX**, the open-source, web-based interface for Ansible, which allows for visual management of playbooks, inventory, and workflows, enhancing collaboration and simplifying complex automation tasks.

---

## Project Goals

The main objectives of this project are:

1. **Environment Setup**: Configure a control node (where Ansible is installed) and two target nodes for automation.
2. **Ansible Automation**: Practice Ansible basics, including managing users, SSH key distribution, and running service-related playbooks.
3. **AWX Exploration**: Gain experience with AWX, including managing jobs and workflows in a web-based UI.
4. **Documentation and Reflection**: Document the development process and reflect on key learnings for future reference.

---

## Technologies Used

- **Ansible**: Used for automating infrastructure setup and configuration.
- **Docker**: Employed to create isolated environments for the control node and target nodes.
- **AWX**: A UI and REST API that provides a user-friendly way to manage Ansible playbooks, inventory, and more.
- **HTTPD (Apache)**: Configured on target nodes to demonstrate service management with Ansible.
- **MacOS**: Development environment (control node setup and Docker orchestration).

---

## Prerequisites

- **macOS**: This project is designed for users working on macOS, though it can be adapted to other systems.
- **Docker**: Used to create control and target nodes in a simulated environment.
- **Ansible**: Installed on the control node for running playbooks.
- **SSH Key Pair**: Needed for secure SSH communication between nodes.

---

## Setup Guide

### Step 1: Simulate Control and Target Nodes

Using Docker, we set up one control node and two target nodes to simulate a multi-host environment. Alternatively, Vagrant or virtual machines could be used.

1. **Launch Docker containers** for each node with names `control_node`, `target_node1`, and `target_node2`.
2. **Install Ansible** on the control node.
3. **Install HTTPD** on the target nodes for testing playbooks.

### Step 2: User Setup for Ansible Automation

To enable Ansible to manage the target nodes, we need a dedicated Ansible user with sudo privileges:

1. **Create the Ansible user** on each target node.
2. **Grant sudo access** to the Ansible user.
3. **Set a password** for the Ansible user to enable SSH key-based login.

### Step 3: SSH Key Configuration

1. **Generate SSH keys** for the Ansible user on the control node:
   ```bash
   ssh-keygen -t rsa -b 2048
   ```
2. **Copy the SSH key** to both target nodes to allow passwordless login:
   ```bash
   ssh-copy-id ansible_user@target_node1
   ssh-copy-id ansible_user@target_node2
   ```
3. **Verify SSH connections**:
   ```bash
   ssh ansible_user@target_node1
   ssh ansible_user@target_node2
   ```

### Step 4: Configuring the Inventory File

Define your target nodes in an inventory file (e.g., `inventory.ini`), specifying IP addresses or container names.

```ini
[webservers]
target_node1 ansible_host=<IP or hostname>
target_node2 ansible_host=<IP or hostname>
```

### Step 5: Testing Connectivity

From the control node, run the following to verify connectivity:

```bash
ansible -i inventory.ini all -m ping
```

---

## Ansible Playbooks Overview

Ansible playbooks used in this project include:

1. **User Management**: Ensures the Ansible user is present on all target nodes.
2. **Service Management**:
   - **HTTPD Installation and Configuration**: Installs and configures Apache on target nodes.
   - **Service Start/Stop**: Starts and stops the Apache service on target nodes.

### Sample Playbook for HTTPD Setup

```yaml
- name: Ensure Apache is installed and running
  hosts: webservers
  become: yes
  tasks:
    - name: Ensure Apache is installed
      package:
        name: "{{ 'httpd' if ansible_os_family == 'RedHat' else 'apache2' }}"
        state: present

    - name: Ensure Apache is started
      service:
        name: "{{ 'httpd' if ansible_os_family == 'RedHat' else 'apache2' }}"
        state: started
```

### AWX Integration

AWX is integrated to enable visual orchestration and management of Ansible jobs and workflows. Key features used include:

1. **Inventory Management**: Manage multiple host groups and configurations from the AWX interface.
2. **Job Templates**: Create reusable job templates that specify playbooks, inventories, and credentials.
3. **Scheduling**: Use AWX’s scheduler to automate routine tasks on target nodes.
4. **Workflows**: Chain jobs together for complex workflows, such as provisioning and configuration in a single pipeline.

### AWX Setup

To install AWX, use Docker or Kubernetes. For this project, Docker Compose is used to simplify the setup.

1. **Clone the AWX repository** and navigate to the installer directory.
2. **Run the installation** with Docker Compose:
   ```bash
   docker-compose up -d
   ```
3. **Access AWX** at `http://localhost:80` and configure your settings (users, inventory, projects, etc.).

---

## Lessons Learned and Reflections

This project was a valuable learning experience in the following areas:

1. **User and Permission Management**: The process of setting up and managing user access on multiple target nodes.
2. **Playbook Design**: Creating modular, reusable playbooks to handle a range of tasks and environments.
3. **AWX for Visual Management**: AWX enhances Ansible with a GUI, allowing easier job management, and provides role-based access control for larger teams.
4. **Troubleshooting**: Learned to debug common issues such as SSH connectivity and service states, especially when working within Docker containers.

---

## Next Steps

Future improvements for this project may include:

1. **Extending Playbooks**: Adding playbooks for more complex deployments and configurations.
2. **Security Hardening**: Implementing secure configurations for SSH, sudo privileges, and service management.
3. **Exploring AWX Features**: Delving deeper into advanced AWX functionalities, such as RBAC and custom inventory plugins.

---

## Conclusion

This project demonstrates core Ansible automation techniques, from user setup to complex service management with HTTPD, and incorporates AWX to visualize and orchestrate these tasks. It’s a hands-on practice for mastering Ansible DevOps practices and serves as a foundation for more advanced automation projects.
