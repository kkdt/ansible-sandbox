# ansible-sandbox

# Overview

This project experiments with Ansible as a deployment tool - building the development (and production) environments for any project. In addition, it provides a way to build a base docker image for Java applications.

# Quickstart

1. Download the [Ansible tarball](https://releases.ansible.com/ansible/?extIdCarryOver=true&sc_cid=701f2000001OH7YAAW) and drop it in the root level of this project.

2. Execute: `vagrant up student`

3. Execute: `vagrant ssh student`

4. Inside Vagrant: `/vagrant/deploy vagrant` (password is vagrant)

5. Browse Nexus on your workstation browser - http://localhost:9081

# Ansible Provisioning

1. Common - application groups and users, set up ssh keys for all users

2. Nexus - [ansible-nexus-oss](https://github.com/savoirfairelinux/ansible-nexus3-oss) forked version to handle new Nexus versions (included with this baseline for now) nexus-oss commit c91fa75

# References

1. https://docs.ansible.com/ansible/latest/modules/include_role_module.html

2. https://docs.ansible.com/ansible/latest/user_guide/intro_dynamic_inventory.html#using-inventory-directories-and-multiple-inventory-sources
