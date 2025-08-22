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
