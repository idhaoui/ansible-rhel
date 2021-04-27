# Workshop Exercise - RHEL System Roles

**Read this in other languages**:
<br>![uk](../images/uk.png) [English](README.md),  ![japan](../images/japan.png)[日本語](README.ja.md), ![brazil](../images/brazil.png) [Portugues do Brasil](README.pt-br.md), ![france](../images/fr.png) [Française](README.fr.md),![Español](../images/col.png) [Español](README.es.md).

## Table des matières

* [Objective](#objective)
* [Guide](#guide)
* [Étape 1 - Installer le package rhel-system-roles ](#step-1---install-rhel-system-roles-package)

## Objectifs

Red Hat Enterprise Linux inclut "Ansible System Roles" pour automatiser les opérations courantes, et la configuration de Red Hat Enterprise Linux.

Dans cet exercice vous allez:

* Prendre connaissance des RHEL System Roles, inclus dans Red Hat Enterprise Linux
* Installer les RHEL System Roles
* Utiliser un RHEL system roles pour configurer la synchronisation temporelle d'un noeud

## Guide

### Etape 1 - Installer  RHEL System Roles

Pour commencer, lister  les roles disponibles sur le système:

```bash
[student<X@>ansible ~]$ ansible-galaxy list
```

Installer le package rhel-system-roles 

```bash
[student<X@>ansible ~]$ sudo yum install rhel-system-roles
```

> **Note**
>
> Red Hat Enterprise Linux 8 utilise, par defaut, dnf en tant que gestionnaire des paquets. Cependant, l'alias 'yum' reste disponible.


Nous pouvons à présent lister les nouveaux RHEL System Roles installés sur le système :

```bash
[student<X@>ansible ~]$ ansible-galaxy list
```

### Etape 2 - Créé un playbook **configure_time.yml**

```yaml
---
- name: Time sync configuration
  hosts: web
  become: true

  roles:
    - rhel-system-roles.timesync
```

### Etape 3 - Adapter les variables du role 

Regarder la section 'Variables' du role rhel-system-roles.timesync documentée dans le README :

```bash
[student<X@>ansible ~]$ cat /usr/share/doc/rhel-system-roles/timesync/README.md
```

Nous allons utiliser les variables disponibles pour mettre en place la configuration désirée :

Pour commencer, créer le répertoire group_vars/all :

```bash
[student<X@>ansible ~]$ mkdir -pv group_vars/all
```

Créer le fichier group_vars/all/timesync.yml:

```yaml
---
#rhel-system-roles.timesync variables for all hosts

timesync_ntp_provider: chrony

timesync_ntp_servers:
  - hostname: ntp1.net.berkeley.edu
    iburst: yes
  - hostname:  ntp2.net.berkeley.edu
    iburst: yes
```

### Etape 4 - Deployer

Lancer le  playbook !

Quelques tâches peuvent générer des warning/erreurs car elles essaient d'arreter des services qui peuvent ne pas être présents sur le système.
Par exemple, le rôle timesync va forcer l'arrêt du service NTP, qui est aujourd'hui obsolète. Il ne faut donc pas en tenir compte. 

```bash
[student<X@>ansible ~]$ ansible-playbook configure_time.yml
```

Vérifier que les changements ont été appliqués:

```bash
[student<X@>ansible ~]$ ansible web -m command -a 'chronyc sources'
```

---
**Navigation**
<br>
[Previous Exercise](../1.7-role)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md)
