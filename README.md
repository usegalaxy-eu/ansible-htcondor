ansible-htcondor
================
Ansible role to install and configure a HTCondor system on a CentOS node.

Requirements
------------
Ansible >= 2.11

Role Variables
--------------
See [defaults/main.yml](defaults/main.yml).

Installation
------------

Install the collection via ansible-galaxy:

`ansible-galaxy install usegalaxy_eu.htcondor`

Playbook usage example
-------------

```yaml
- name: Install and configure HTCondor
  hosts: all
  vars:
    condor_fs_domain: my_own_string
    condor_uid_domain: my_own_string
  roles:
    - usegalaxy_eu.htcondor
```

License
-------
GPLv3

Author Information
------------------
[Galaxy Europe](https://galaxyproject.eu)
