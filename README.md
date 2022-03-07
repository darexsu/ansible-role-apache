# Ansible role apache
[![CI Molecule](https://github.com/darexsu/ansible-role-apache/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-apache/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/58260?color=blue&label=downloads)

|  Testing         |  Official repo     |
| :--------------: | :----------------: |
| Debian 11        |  apache  2.4       |
| Debian 10        |  apache  2.4       |
| Ubuntu 20.04     |  apache  2.4       |
| Ubuntu 18.04     |  apache  2.4       |
| Oracle Linux 8   |  apache  2.4       |
| Rocky Linux 8    |  apache  2.4       |

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

Replace or Merge dictionaries (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# How does merge work?
Your vars [host_vars]  -->  default vars [current role] --> default vars [include role]
  
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
      # Apache
      apache:
        enabled: true
      # Apache -> install
      apache_install:
        enabled: true
      # Apache -> config
      apache_conf:
        enabled: true
        backup: true
      # Apache -> config -> virtualhost
      apache_virtualhost:
      # Apache -> config -> virtualhost -> delete default virtual_host
        default_conf:
          enabled: true
          state: "absent"
      # Apache -> config -> virtualhost -> add new virtual_host
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
            ErrorLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/error.log"
            CustomLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/access.log combined"
            SSLEngine: ""
            SSLCertificateFile: ""
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
      # Apache
      apache:
        enabled: true
      # Apache -> install
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
      # Apache
      apache:
        enabled: true
      # Apache -> config -> apache.conf
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
      # Apache
      apache:
        enabled: true
      # Apache -> config -> virtualhost
      apache_virtualhost:
      # Apache -> config -> virtualhost -> delete default virtual_host
        default_conf:
          enabled: true
          state: "absent"
      # Apache -> config -> virtualhost -> add new virtual_host
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
            ErrorLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/error.log"
            CustomLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/access.log combined"
            SSLEngine: ""
            SSLCertificateFile: ""
            SSLCertificateKeyFile: ""
              
  tasks:
  - name: role darexsu.apache
    include_role: 
      name: darexsu.apache
```