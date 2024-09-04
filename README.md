# Ansible Omeka S
[![CI](https://github.com/supertarto/ansible-omeka-S/actions/workflows/ci.yml/badge.svg)](https://github.com/supertarto/ansible-omeka-S/actions/workflows/ci.yml)
Install and configure Omeka S with Ansible.


## Requirements
A web server, php and MariaDB. You can use supertarto.apache, supertarto.mariadb and supertarto.php

## Tested plateform
* Debian 11 (Bullseye)
* Debian 12 (Bookworm)

## Role variables
Force omeka update
```yml
omekaS_force_update: false
```
Define wich version to download, the download link, where to unarchive and the destination.
```yml
omekaS_release_version: "4.0.4"
omekaS_download_url: "https://github.com/omeka/omeka-s/releases/download/v{{ omekaS_release_version }}/omeka-s-{{ omekaS_release_version }}.zip"
omekaS_unarchive_dir: "/var/www"
omekaS_content_dest: "/var/www/omeka/"
```
The directory where your local configurations will be backed up. Used only with omekaS_force_update set to True
```yml
omekaS_backup_directory: /"usr/local/omeka-bck"
```
The web user and group
```yml
omekaS_web_owner: www-data
omekaS_web_group: www-data
```
Used in database.ini
```yml
omekaS_db_user: omeka
omekaS_db_password: omekapass
omekaS_db_name: omekadb
omekaS_db_host: localhost
```

## Examples
```yml
hosts: somehost
roles:
- role: supertarto.apache
    - role: supertarto.mariadb
    - role: supertarto.nodejs
    - role: supertarto.php
    - role: supertarto.omeka_s
vars:
php_packages:
    - php8.2
    - php8.2-mysql
    - php8.2-pdo
    - php8.2-xml
    - php8.2-mbstring
    - php8.2-intl

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
          priv: "{{ omeka_db_name }}.*:SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER,CREATE TEMPORARY TABLES,LOCK TABLES"

    omekaS_release_version: "4.0.4"
    omekaS_db_user: omeka
    omekaS_db_password: omekapass
    omekaS_db_name: omekadb
    omekaS_db_host: localhost
```

## Installation
```
ansible-galaxy role install supertarto.omeka_s
```
## License
GPL V3.0
