# Ansible role apache
[![CI Molecule](https://github.com/darexsu/ansible-role-apache/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-apache/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57564?color=blue&label=downloads)

|  Testing         |  Debian            |  Ubuntu         |  Rocky Linux  | Oracle Linux |
| :--------------: | :----------------: | :-------------: | :-----------: | :----------: |
| Distro version   |  10, 11            | 18.04, 20.04    |  8            | 8            |
| apache version  |  2.4.x+            |    2.4.x+       |  2.4.x+       | 2.4.x+       |

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.apache --force
```

### 2) Example playbooks:
  
  - [full playbook](#full-playbook)  
    - install
      - [official repo](#install-from-official-repo)
    - config
      - [apache.conf](#apacheconf)
      - [delete defaults and add new virtualhost.conf](#delete-defaults-and-add-new-virtualhostconf)

Role behaviour: Replace or Merge (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
[host_vars]           [host_vars]
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# Role recursive merge:
[host_vars]     [current role]    [include_role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```
##### Full playbook
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
    # ┌┄┄┄┄┄┄┄┄                 Apache 
      apache:
        enabled: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄          install apache
      apache_install:
        enabled: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄          configure apache.conf
      apache_conf:
        enabled: true
        backup: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄          virtual_host section
      apache_virtualhost:
      # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄          delete default virtual_host
        default_conf:
          enabled: true
          state: "absent"
      # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄           add new virtual_host
        new_conf:
          enabled: true    
          file: "new.conf"
          state: "present"
          src: "apache__virtualhost.j2"
          backup: false
          vars:
            ip: "*"
            port: "80"
            ServerName: "www.example.com"
            ServerAdmin: "webmaster@localhost"
            DocumentRoot: "/var/www/html"
            ErrorLog: "/var/log/{{ apache.service.name }}/error.log"
            CustomLog: "/var/log/{{ apache.service.name }}/access.log combined"
            SSLEngine: ""
            SSLCertificateFile:	""
            SSLCertificateKeyFile: ""
  
  tasks:
  - name: role darexsu.apache
    include_role: 
      name: darexsu.apache
    
```
##### install from official repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
    # ┌┄┄┄┄┄┄┄┄               Apache 
      apache:
        enabled: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄         install apache
      apache_install:
        enabled: true

  tasks:
  - name: role darexsu.apache
    include_role: 
      name: darexsu.apache

```
##### apache.conf
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
    # ┌┄┄┄┄┄┄┄┄               Apache 
      apache:
        enabled: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄         configure apache.conf
      apache_conf:
        enabled: true
        backup: true

  
  tasks:
  - name: role darexsu.apache
    include_role: 
      name: darexsu.apache

```
##### delete defaults and add new virtualhost.conf
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
    # ┌┄┄┄┄┄┄┄┄                 Apache 
      apache:
        enabled: true
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄          virtual_host section
      apache_virtualhost:
      # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄          delete default virtual_host
        default_conf:
          enabled: true
          state: "absent"
      # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄          add new virtual_host
        new_conf:
          enabled: true    
          file: "new.conf"
          state: "present"
          src: "apache__virtualhost.j2"
          backup: false
          vars:
            ip: "*"
            port: "80"
            ServerName: "www.example.com"
            ServerAdmin: "webmaster@localhost"
            DocumentRoot: "/var/www/html"
            ErrorLog: "/var/log/{{ apache.service.name }}/error.log"
            CustomLog: "/var/log/{{ apache.service.name }}/access.log combined"
            SSLEngine: ""
            SSLCertificateFile:	""
            SSLCertificateKeyFile: ""
              
  tasks:
  - name: role darexsu.apache
    include_role: 
      name: darexsu.apache
```