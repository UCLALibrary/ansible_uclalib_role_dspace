# UCLALib Ansible Role: DSpace [![Build Status](https://travis-ci.org/UCLALibrary/uclalib_role_dspace.svg?branch=master)](https://travis-ci.org/UCLALibrary/uclalib_role_dspace)

Creates a bare-bones installation/configuration of a DSpace repository server

This role is loosely based on the [Ansible Fedora 4 role created by the Digital Repository of Ireland ](https://github.com/Digital-Repository-of-Ireland/ansible-fedora4)

## Dependencies

* uclalib_role_java
* uclalib_role_apache
* uclalib_role_tomcat
* uclalib_role_iptables

## Variables

Most variables are provided as defaults in defaults/main.yml, only two variables are further constructed from defaults:
dspace_install_dir and dspace_source_dir. For details look in vars/main.yml.

## Sample Variable Definition Formats

The variable definitions should be placed in the playbook under the `vars` statement.

It is highly recommended to utilize an [ansible vault-protected vars file](https://www.ansible.com/blog/2014/02/19/ansible-vault) -- see the example below:

```ansible
---
- name: uclalib_dspace_test.yml
  sudo: true
  hosts: test
  vars_files:
    - vars/dspace_vars_private.yml
    - vars/dspace_vars_open.yml

  # Dependencies:
  # ANXS/PostgreSQL role (https://github.com/ANXS/postgresql)testing...

  vars:
    java_package: java-1.7.0-openjdk-devel.x86_64

    postgresql_version: 9.5
    postgresql_encoding: 'UTF-8'
    postgresql_locale: 'en_US.UTF-8'
    postgresql_ctype: 'en_US.UTF-8'
    postgresql_admin_user: "postgres"
    postgresql_default_auth_method: "trust"
    postgresql_service_enabled: true

    # create a postgresql user for dspace
    postgresql_users:
      - name: "{{ dspace_db_username }}"
        pass: "{{ dspace_db_password }}"
        encrypted: no

    # create a database for dspace
    postgresql_databases:
      - name: "{{ dspace_db_name }}"
        owner: "{{ dspace_db_username }}"
        encoding: 'UTF-8'
        lc_collate: 'en_US.UTF-8'
        lc_ctype: 'en_US.UTF-8'

    # OPTIONAL: you can specify database extensions here (like pgcrypto, which is required for DSapce 6)
    # postgresql_database_extensions:
    #    - db: "{{ dspace_db_name }}"
    #      extensions:
    #          - pgcrypto

    # database privileges for the dspace user
    postgresql_user_privileges:
      - name: "{{ dspace_db_username }}"
        db: "{{ dspace_db_name }}"
        priv: "ALL"
        role_attr_flags: "CREATEDB"

    # drop a pgpass file in our own user folder, to make working with PostgreSQL via the command line slightly easier
    template: src=templates/dspace_pgpass.j2 dest="~/.pgpass" mode=0600

  roles:
    - { role: uclalib_role_java }
    - { role: uclalib_role_vim }
    - { role: uclalib_role_clamav }
    - { role: uclalib_role_java_maven }
    - { role: uclalib_role_java_ant }
    - { role: uclalib_role_git }
    - { role: anxs_role_postgresql }
    - { role: uclalib_role_apache }
    - { role: uclalib_role_tomcat }
    - { role: uclalib_role_dspace }
```

You can override the defaults by including them in the play (see above for examples).

## Contributing

Pull requests gladly accepted, thanks!
