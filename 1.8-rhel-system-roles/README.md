# Workshop Exercise - RHEL System Roles

**Read this in other languages**:
<br>![uk](../images/uk.png) [English](README.md),  ![japan](../images/japan.png)[日本語](README.ja.md), ![brazil](../images/brazil.png) [Portugues do Brasil](README.pt-br.md), ![france](../images/fr.png) [Française](README.fr.md),![Español](../images/col.png) [Español](README.es.md).

## Table of Contents

* [Objective](#objective)
* [Guide](#guide)
* [Step 1 - Install rhel-system-roles Package](#step-1---install-rhel-system-roles-package)

## Objective

Red Hat Enterprise Linux includes Ansible system roles to simplify automation of common operations, and configuration of Red Hat Enterprise Linux subsystems.

In this exercise you will:

* Learn about RHEL System Roles included with Red Hat Enterprise Linux
* Configure a host to use RHEL System Roles
* Use one of the RHEL system roles in conjunction with a normal task to configure time synchronization and the time zone on your server

## Guide

### Step 1 - List currently available roles on the system

```bash
[student<X@>ansible ~]$ ansible-galaxy list
```

> **Note**
>
> Red Hat Enterprise Linux 8 uses the dnf package manager and shell command. However, yum aliases are available for convenience.

### Step 2 - Install rhel-system-roles Package

```bash
[student<X@>ansible ~]$ sudo yum install rhel-system-roles
```

### Step 3 - List newly available RHEL System Roles on the system

```bash
[student<X@>ansible ~]$ ansible-galaxy list
```

### Step 4 - Create a playbook **configure_time.yml**

```yaml
---
- name: Time sync configuration
  hosts: web

  roles:
    - rhel-system-roles.timesync
```
---
**Navigation**
<br>
[Previous Exercise](../1.7-role)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md)
