# Ansible Omeka
[![Build Status](https://travis-ci.org/supertarto/ansible-omeka.svg?branch=master)](https://travis-ci.org/supertarto/ansible-omeka)

Install and configure Omeka with Ansible. For now, only work with Omeka-S

## Requirements
A web server, php and MariaDB. You can use supertarto.apache, supertarto.mariadb and supertarto.php

## Tested plateform
* Debian 10 (Buster)

## Role variables
Force omeka update
```yml
omeka_force_update: false
```
Define the git directory, the web content directory and the git branch to use.
```yml
omeka_separate_git_dir: "/usr/local/omeka-git"
omeka_content_dest: "/var/www/omeka/"
omeka_git_branch_version: "master"
```
Used in database.ini
```yml
omeka_db_user: omeka
omeka_db_password: omekapass
omeka_db_name: omekadb
omeka_db_host: localhost
```

## Examples
```yml
hosts: somehost
roles:
- role: supertarto.apache
    - role: supertarto.mariadb
    - role: supertarto.nodejs
    - role: supertarto.php
    - role: supertarto.omeka
vars:
php_packages:
    - php7.3
    - php7.3-mysql
    - php7.3-pdo
    - php7.3-xml
    - php7.3-mbstring
    - php7.3-intl

apache_create_vhosts: true
apache_vhosts_filename: "omeka.conf"
apache_vhost_config:
    - listen_ip: "*"
    listen_port: 80
    server_name: host1
    documentroot: "{{ omeka_content_dest }}"
    serveradmin: admin@localhost
    custom_param: |
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        LogLevel warn
    directory:
        - path: "{{ omeka_content_dest }}"
          config: |
            AllowOverride All
            Order deny,allow
            allow from all

    mariadb_use_dump_script: false
    mariadb_databases:
        - name: "{{ omeka_db_name }}"

    mariadb_users:
        - name: "{{ omeka_db_user }}"
          host: "{{ omeka_db_host }}"
          password: "{{ omeka_db_password }}"
          priv: "{{ omeka_db_name }}.*:SELECT,INSERT,   UPDATE,DELETE,CREATE,DROP,ALTER,CREATE TEMPORARY TABLES,LOCK TABLES"

    omeka_db_user: omeka
    omeka_db_password: omekapass
    omeka_db_name: omekadb
    omeka_db_host: localhost
```

## Installation
```
ansible-galaxy install supertarto.omeka
```
## License
GPL V3.0
