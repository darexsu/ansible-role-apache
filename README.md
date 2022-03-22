# Ansible role Apache
[![CI Molecule](https://github.com/darexsu/ansible-role-apache/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-apache/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/58260?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [merge behaviour](#merge-behaviour)
  - Playbooks (merge version):
      - [install and configure: Apache](#install-and-configure-apache-merge-version)
          - [install: Apache from official repo](#install-apache-from-official-repo-merge-version)
          - [configure: apache.conf](#configure-apacheconf-merge-version)
          - [configure: delete default vhost.conf and add new vhost.conf](#configure-delete-default-vhostconf-and-add-new-vhostconf-merge-version)
          - [configure: enable/disable mods ](#configure-enabledisable-mods-merge-version)
  - Playbooks (full version):
      - [install and configure: Apache](#install-and-configure-apache-full-version)
          - [install: Apache from official repo](#install-apache-from-official-repo-full-version)
          - [configure: apache.conf](#configure-apacheconf-full-version)
          - [configure: delete default vhost.conf and add new vhost.conf](#configure-delete-default-vhostconf-and-add-new-vhostconf-full-version)
          - [configure: enable/disable mods ](#configure-enabledisable-mods-full-version)

### Platforms

|  Testing         |  Official repo     |
| :--------------: | :----------------: |
| Debian 11        |  apache  2.4       |
| Debian 10        |  apache  2.4       |
| Ubuntu 20.04     |  apache  2.4       |
| Ubuntu 18.04     |  apache  2.4       |
| Oracle Linux 8   |  apache  2.4       |
| Rocky Linux 8    |  apache  2.4       |

### Install
```
ansible-galaxy install darexsu.apache --force
```

### Merge behaviour

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
##### Install and configure: Apache (merge version)
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
      # Apache -> config -> apache.conf
      apache_conf:
        enabled: true
        data:
          apache_user: "www-data"
          apache_group: "www-data"
      # Apache -> config -> modules
      apache_modules:
        enabled: true
        mods_enabled: [rewrite]
        mods_disabled: []
      # Apache -> config -> {virtualhost}.conf
      apache_virtualhost:
        default_conf:
          enabled: true
          state: "present"
          data:
            ip: "*"
            port: "80"
            ServerName: "www.example.com"
            ServerAdmin: "webmaster@localhost"
            DocumentRoot: "/var/www/html"
            ErrorLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/error.log"
            CustomLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/access.log combined"
  
  tasks:
    - name: role darexsu.apache
      include_role: 
        name: darexsu.apache
    
```
##### Install: Apache from official repo (merge version)
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
##### Configure: apache.conf (merge version)
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
        file: "{{ apache_const[ansible_os_family]['conf_file'] }}"
        src: "{{ apache_const[ansible_os_family]['conf_src'] }}"
        backup: false
        data:
          apache_user: "www-data"
          apache_group: "www-data"
  
  tasks:
    - name: role darexsu.apache
      include_role: 
        name: darexsu.apache

```
##### Configure: delete default vhost.conf and add new vhost.conf (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Apache
      apache:
        enabled: true
      # Apache -> config -> {virtualhost}.conf
      apache_virtualhost:
        default_conf:
          enabled: true
          file: "{{ apache_const[ansible_os_family]['virtualhost_default_file'] }}"
          state: "absent"
          src: "apache_virtualhost.j2"
          backup: false
          data:
            ip: "*"
            port: "80"
            ServerName: "www.example1.com"
            ServerAdmin: "webmaster@localhost"
            DocumentRoot: "/var/www/html"
            ErrorLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/error.log"
            CustomLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/access.log combined"
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "apache_virtualhost.j2"
          backup: false
          data:
            ip: "*"
            port: "80"
            ServerName: "www.example2.com"
            ServerAdmin: "webmaster@localhost"
            DocumentRoot: "/var/www/html2"
            ErrorLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/error.log"
            CustomLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/access.log combined"
              
  tasks:
    - name: role darexsu.apache
      include_role: 
        name: darexsu.apache
```
##### Configure enable/disable mods (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Apache
      apache:
        enabled: true
      # Apache -> config -> modules
      apache_modules:
        enabled: true
        mods_enabled: [rewrite]
        mods_disabled: []
              
  tasks:
    - name: role darexsu.apache
      include_role: 
        name: darexsu.apache
```
##### Install and configure: Apache (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Apache
      apache:
        enabled: true
        service:
          enabled: true
          state: "started"
      # Apache -> install
      apache_install:
        enabled: true
        packages:
          Debian: [apache2]
          RedHat: [httpd]
        dependencies:
          Debian: []
          RedHat: []
      # Apache -> config -> apache.conf
      apache_conf:
        enabled: true
        file: "{{ apache_const[ansible_os_family]['conf_file'] }}"
        src: "{{ apache_const[ansible_os_family]['conf_src'] }}"
        backup: false
        data:
          apache_user: "www-data"
          apache_group: "www-data"
      # Apache -> config -> modules
      apache_modules:
        enabled: true
        mods_enabled: [rewrite]
        mods_disabled: []
      # Apache -> config -> {virtualhost}.conf
      apache_virtualhost:
        default_conf:
          enabled: true
          file: "{{ apache_const[ansible_os_family]['virtualhost_default_file'] }}"
          state: "present"
          src: "apache_virtualhost.j2"
          backup: false
          data:
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
##### Install: Apache from official repo (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Apache
      apache:
        enabled: true
        service:
          enabled: true
          state: "started"
      # Apache -> install
      apache_install:
        enabled: true
        packages:
          Debian: [apache2]
          RedHat: [httpd]
        dependencies:
          Debian: []
          RedHat: []

  tasks:
    - name: role darexsu.apache
      include_role: 
        name: darexsu.apache

```
##### Configure: apache.conf (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Apache
      apache:
        enabled: true
        service:
          enabled: true
          state: "started"
      # Apache -> config -> apache.conf
      apache_conf:
        enabled: true
        file: "{{ apache_const[ansible_os_family]['conf_file'] }}"
        src: "{{ apache_const[ansible_os_family]['conf_src'] }}"
        backup: false
        data:
          apache_user: "www-data"
          apache_group: "www-data"
  
  tasks:
    - name: role darexsu.apache
      include_role: 
        name: darexsu.apache

```
##### Configure: delete default vhost.conf and add new vhost.conf (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Apache
      apache:
        enabled: true
        service:
          enabled: true
          state: "started"
      # Apache -> config -> {virtualhost}.conf
      apache_virtualhost:
        default_conf:
          enabled: true
          file: "{{ apache_const[ansible_os_family]['virtualhost_default_file'] }}"
          state: "absent"
          src: "apache_virtualhost.j2"
          backup: false
          data:
            ip: "*"
            port: "80"
            ServerName: "www.example1.com"
            ServerAdmin: "webmaster@localhost"
            DocumentRoot: "/var/www/html"
            ErrorLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/error.log"
            CustomLog: "/var/log/{{ apache_const[ansible_os_family]['service_name'] }}/access.log combined"
            SSLEngine: ""
            SSLCertificateFile: ""
            SSLCertificateKeyFile: ""
      # Apache -> config -> new.conf
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "apache_virtualhost.j2"
          backup: false
          data:
            ip: "*"
            port: "80"
            ServerName: "www.example2.com"
            ServerAdmin: "webmaster@localhost"
            DocumentRoot: "/var/www/html2"
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
##### Configure enable/disable mods (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Apache
      apache:
        enabled: true
        service:
          enabled: true
          state: "started"
      # Apache -> config -> modules
      apache_modules:
        enabled: true
        mods_enabled: [rewrite]
        mods_disabled: []
              
  tasks:
    - name: role darexsu.apache
      include_role: 
        name: darexsu.apache
```