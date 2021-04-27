# Workshop Exercise - RHEL System Roles

**Read this in other languages**:
<br>![uk](../images/uk.png) [English](README.md),  ![japan](../images/japan.png)[日本語](README.ja.md), ![brazil](../images/brazil.png) [Portugues do Brasil](README.pt-br.md), ![france](../images/fr.png) [Française](README.fr.md),![Español](../images/col.png) [Español](README.es.md).

## Table of Contents

* [Objective](#objective)
* [Guide](#guide)
* [Step 1 - Install rhel-system-roles Package](#step-1---install-rhel-system-roles-package)

## Objective

Red Hat Enterprise Linux includes Ansible system roles to simplify automation of common operations, and configuration of Red Hat Enterprise Linux subsystems.

RHEL System Roles are a collection of Ansible roles and modules that provide a stable and consistent configuration interface to automate and manage multiple releases of Red Hat Enterprise Linux. The effort is based on development of the Linux System Roles upstream project.

The RHEL System Roles are supported as provided from the following methods:
- As an RPM package in the RHEL 7 Extras repository
- As an RPM package in the RHEL 8 application Streams repository
- In the future, as a supported collection in the Red Hat Automation Hub

In this exercise you will:

* Learn about RHEL System Roles included with Red Hat Enterprise Linux
* Configure a host to use RHEL System Roles
* Use one of the RHEL system roles in conjunction with a normal task to configure time synchronization and the time zone on your server

## Guide

### Step 1 - Install RHEL System Roles

First, list currently available roles on the system:

```bash
[student<X@>ansible ~]$ ansible-galaxy list
```

Now let's install the rhel-system-roles package

```bash
[student<X@>ansible ~]$ sudo yum install rhel-system-roles
```

> **Note**
>
> Red Hat Enterprise Linux 8 uses the dnf package manager and shell command. However, yum aliases are available for convenience.


We can now list newly available RHEL System Roles on the system with the first command we used:

```bash
[student<X@>ansible ~]$ ansible-galaxy list
```

### Step 2 - Create a playbook **configure_time.yml**

```yaml
---
- name: Time sync configuration
  hosts: web
  become: true

  roles:
    - rhel-system-roles.timesync
```

### Step 3 - Adapt the variables used within the role to suit your needs

Review the Role Variables section of the README.md file for the rhel-system-roles.timesync role:

```bash
[student<X@>ansible ~]$ cat /usr/share/doc/rhel-system-roles/timesync/README.md
```

We will need to pick from the available variables to apply the desired configuration.

First, create the group_vars/all subdirectory:

```bash
[student<X@>ansible ~]$ mkdir -pv group_vars/all
```

Create a new file group_vars/all/timesync.yml:

```yaml
---
#rhel-system-roles.timesync variables for all hosts

timesync_ntp_provider: chrony

timesync_ntp_servers:
  - hostname: ntp1.net.berkeley.edu
    iburst: yes
  - hostname: ntp2.net.berkeley.edu
    iburst: yes
```

### Step 4 - Try it out

Run the playbook !

Some tasks will result in errors, because they attempt to stop services that are not running in this exercise. The timesync system role would typically expect to find and stop the deprecated NTP services. This is perfectly normal.

```bash
[student<X@>ansible ~]$ ansible-playbook configure_time.yml
```

Make sure your changes were applied:

```bash
[student<X@>ansible ~]$ ansible web -m command -a 'chronyc sources'
```

---
**Navigation**
<br>
[Previous Exercise](../1.7-role)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md)
