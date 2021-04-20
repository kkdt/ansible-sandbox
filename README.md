# ansible-sandbox

# Overview

This project experiments with Ansible as a deployment tool - building an environment for a software application suite.

# Quickstart

1. Bring up all servers

    ```bash
    $ vagrant up
    ```

2. Provision the dev00 server with Ansible

    ```bash
    $ vagrant provision dev00 --provision-with=ansible
    ```

3. Establish trust for the `vagrant` user between all servers

    ```bash
    $ vagrant provision dev00 --provision-with=trust
    $ vagrant provision bs01 --provision-with=trust
    $ vagrant provision db01 --provision-with=trust
    $ vagrant provision ldap01 --provision-with=trust
    $ vagrant provision ss01 --provision-with=trust
    $ vagrant provision ss02 --provision-with=trust
    ```
    
    or
    
    ```bash
    $ vagrant provision  --provision-with=trust
    ```

4. Log into a server and confirm you can ssh into all the other servers without password. For example, 

    ```bash
    $ vagrant ssh dev00
    [vagrant@dev00] $ ssh bs01
    ```

5. To kill all servers: `vagrant destroy -f`

# Ansible

This project sets up the following servers for Ansible to run playbooks against.

| Server    | Description                               | Configuration |
| :---      | :---                                      | :---          |
| dev00     | The workstation with Ansible installed    | [dev00.json](vagrant/dev00.json) |
| bs01      | Server   | [bs01.json](vagrant/bs01.json) |
| db01      | Server   | [db01.json](vagrant/db01.json) |
| ldap01    | Server   | [ldap01.json](vagrant/ldap01.json) |
| ss01      | Server   | [ss01.json](vagrant/ss01.json) |
| ss02      | Server   | [ss02.json](vagrant/ss02.json) |
