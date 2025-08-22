# Ansible

- Linux  
- Shell scripting  
- SCM tools: Git *(GitHub, Bitbucket, GitLab)*  
- Build tools: Maven, Gradle, Ant  
- Code quality: SonarQube  
- Artifactory / Repository tools: Nexus, JFrog  
- Application servers: Tomcat, JBoss, WebLogic  
- Web servers: Apache HTTPD, Nginx, HAProxy  
- CI/CD tools: Jenkins, Bamboo, Travis CI  
- Containerization tools: Docker, Rocket  
- Container orchestration tools: Kubernetes (K8s), OpenShift, Docker Swarm  
- Configuration management tools: **Ansible, Puppet, Chef**  

> **Note:** Using configuration management tools, we can configure *N* number of servers using a single machine (configuration server).  

---

# Introduction

- Ansible is an **open-source Configuration Management and Deployment tool**, maintained by Red Hat.  
- The main components of Ansible are **playbooks, configuration management, and deployment**.  
- Ansible uses **playbooks** to deploy, manage, build, test, and configure anything from full server environments to custom compiled source code for applications.  
- Ansible is written in **Python**.  

---

# Configuration Tasks

Configuration can be any task performed on servers (hosts), such as:

1. Install / Update / Uninstall software (packages)  
2. Copy files and change permissions on files or directories  
3. Start / Stop / Restart services  
4. Create / Delete users  

---

# Various Configuration Management Tools

- Ansible  
- Chef  
- Puppet  
- SaltStack  

---

# Ansible Features

- Agentless configuration using **SSH**  
- Built on Python → provides Python functionality  
- **YAML-based Playbooks**  
- Uses **push-based** architecture  

### Push vs Pull  
- **Pull based (Puppet, Chef):** Agent on server periodically pulls configuration from Master.  
- **Push based (Ansible):** Central server pushes configuration to target servers. You control when changes are applied.  

---

# Ansible Architecture

🔗 [Ansible Architecture Reference](https://www.google.com/search?q=ansible+architecture)  

---

# Host Inventory

Ansible inventory defines the hosts (machines) that Ansible manages.  

### Key Points
- Comments → `#`  
- Blank lines ignored  
- Groups → `[groupname]`  
- Hosts can belong to multiple groups  
- Ungrouped hosts listed before any groups  

**Example (INI format):**
```ini
192.168.122.1
192.168.122.2
kkdevops.com

[webservers]
192.168.122.2
192.168.122.3
kkdevops.com

[dbservers]
192.168.122.4
192.168.122.5
192.168.122.2
```

### Types of Inventories
1. **Static Inventory** – File maintained manually (`/etc/ansible/hosts`)  
2. **Dynamic Inventory** – Generated dynamically (e.g., cloud APIs)  

---

# Playbooks

Playbooks are **YAML files** that define tasks.  

### Components:
- **Plays** → map hosts to tasks  
- **Tasks** → actions (install, copy, execute)  
- **Modules** → built-in functionality (e.g., `yum`, `copy`)  
- **Variables** → dynamic values  
- **Handlers** → triggered tasks (e.g., restart service)  
- **Conditionals & Loops** → control execution  

**Example:**
```yaml
- name: Install Git on all servers
  hosts: all
  tasks:
    - name: Install Git
      yum:
        name: git
        state: present
```

---

# Core vs Custom Modules

- **Core modules** → shipped with Ansible  
- **Custom modules** → can be written in Python  

---

# Ansible Installation

### Prerequisites:
- OS: Linux  
- Language: Python  

> Note: Ansible can configure Windows machines, but cannot be installed on Windows.  

---

## Setup Example (AWS)

- Ansible server: `172.31.4.33`  
- Host 1: `172.31.4.10`  
- Host 2: `172.31.15.109`  

### Steps:
1. Create users and configure `sudoers` for password-less access  
2. Update `sshd_config` for password authentication  
3. Restart `sshd` service  
4. Install Ansible:  
   ```bash
   sudo yum install ansible-core -y
   ```

5. Configure `/etc/ansible/hosts`:
   ```ini
   [my_hosts]
   172.31.4.10 ansible_ssh_user=ec2-user ansible_ssh_private_key_file=~/kkdevops.pem
   172.31.15.109 ansible_ssh_user=ec2-user ansible_ssh_private_key_file=~/kkdevops.pem
   ```

6. Test connectivity:  
   ```bash
   ansible all -m ping
   ansible all -m ping -o
   ```

---

# Ansible Modules

List modules:  
```bash
ansible-doc -l
ansible-doc copy
ansible-doc yum
```

### Examples

**Shell module:**
```bash
ansible all -m shell -a "df -kh"
ansible all -a "uptime"
```

**Yum module:**
```bash
ansible all -b -m yum -a "name=httpd state=present"
ansible all -b -m yum -a "name=httpd state=absent"
```

**Service module:**
```bash
ansible all -b -m service -a "name=httpd state=started"
```

---

# Playbook Example (Web Server)

```yaml
- hosts: all
  become: true
  tasks:
    - name: Install httpd
      yum:
        name: httpd
        state: present
    - name: Copy index.html
      copy:
        src: index.html
        dest: /var/www/html/index.html
    - name: Start httpd
      service:
        name: httpd
        state: started
```

---

# Variables in Ansible

Priority:
1. Runtime (`-e`)  
2. Variables in file  
3. Variables in playbook  
4. Host variables  
5. Group variables  

### Example (vars.yaml)
```yaml
location: bangalore
phoneNo: 9876543210
```

```yaml
- hosts: all
  vars_files:
    - vars.yaml
  tasks:
    - name: Install java
      yum:
        name: "{{ javaVersion }}"
```

---

# Debug Module Example

```yaml
- name: Debug Example
  hosts: localhost
  vars:
    app_name: "order-management"
  tasks:
    - name: Print app name
      debug:
        msg: "The application is {{ app_name }}"
```

---

# Handlers

- Tasks → always executed  
- Handlers → executed only when notified  

```yaml
tasks:
  - name: Copy config
    copy:
      src: index.html
      dest: /var/www/html/index.html
    notify:
      - restart httpd

handlers:
  - name: restart httpd
    service:
      name: httpd
      state: restarted
```

---

# Loops

```yaml
- name: Install packages
  yum:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - vim
    - unzip
```

---

# When Condition

```yaml
- name: Install Apache
  yum:
    name: httpd
    state: present
  when: ansible_distribution == "RedHat"
```

---

# Tags

```yaml
- name: Install httpd
  yum:
    name: httpd
    state: present
  tags: install
```

Run with tags:  
```bash
ansible-playbook play.yml --tags "install"
ansible-playbook play.yml --skip-tags "configure"
```

---

# Roles

Organized structure:
```
roles/
 ├── apache/
 │   ├── tasks/main.yml
 │   ├── templates/index.html
 │   ├── vars/main.yml
 │   ├── handlers/main.yml
 │   └── defaults/main.yml
```

---

# Ansible Vault

Encrypt sensitive data:
```bash
ansible-vault create secret.yml
ansible-vault edit secret.yml
ansible-vault decrypt secret.yml
```

Use in playbooks:
```yaml
- hosts: all
  vars_files:
    - secret.yml
  tasks:
    - debug:
        msg: "Password: {{ db_password }}"
```

Run with vault:
```bash
ansible-playbook play.yml --ask-vault-pass
```

---

# Dynamic Inventory (AWS Example)

### Create inventory file `aws_ec2.yml`:
```yaml
plugin: amazon.aws.aws_ec2
regions:
  - ap-south-1
filters:
  instance-state-name: running
keyed_groups:
  - key: tags.Name
    prefix: "tag_"
compose:
  ansible_host: public_ip_address
```

Verify:
```bash
ansible-inventory -i aws_ec2.yml --list
```

Run playbook:
```bash
ansible-playbook -i aws_ec2.yml install_apache.yml -u ec2-user --private-key ~/kkdevops.pem
```

---

# ✅ Summary

- Ansible is **agentless**, uses **SSH**, and is **push-based**.  
- Configuration is defined via **playbooks (YAML)**.  
- Supports **variables, loops, handlers, conditionals, roles, tags**.  
- Sensitive data managed via **Ansible Vault**.  
- Supports **dynamic inventory** for cloud environments.  
