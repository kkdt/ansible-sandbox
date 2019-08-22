# ansible-sandbox

# Quickstart

1. Download the [Ansible tarball](https://releases.ansible.com/ansible/?extIdCarryOver=true&sc_cid=701f2000001OH7YAAW) and drop it in the root level of this project.

2. Execute: `vagrant up student`

3. Execute: `vagrant ssh student`

4. Inside Vagrant: `/vagrant/environments/deploy vagrant` (password is vagrant)

Ansible Provisioning

1. Common - application groups and users, set up ssh keys for all users

2. Nexus - [ansible-nexus-oss](https://github.com/savoirfairelinux/ansible-nexus3-oss) forked version to handle new Nexus versions (included with this baseline for now) nexus-oss commit c91fa75
