# ansible-sandbox

# Overview

This project experiments with Ansible as a deployment tool - building the development (and production) environments for any project.

# Quickstart

1. Bring up the all servers

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

4. Log into a server and confirm you can ssh into all the other servers without password. For example, 

    ```bash
    $ vagrant ssh dev00
    [vagrant@dev00] $ ssh bs01
    ```

# Ansible

This project sets up the following servers for Ansible to run playbooks against.

| Server    | Description                               |
| :---      | :---                                      |
| dev00     | The workstation with Ansible installed    |
| bs01      | Server   |
| db01      | Server   |
| ldap01    | Server   |
| ss01      | Server   |
| ss02      | Server   |
