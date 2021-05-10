# -*- mode: ruby -*-
# vi: set ft=ruby :

#
# Copyright 2017 kkdt
# Permission is hereby granted, free of charge, to any person obtaining a copy of
# this software and associated documentation files (the "Software"), to deal in
# the Software without restriction, including without limitation the rights to use,
# copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the
# Software, and to permit persons to whom the Software is furnished to do so, subject
# to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED,
# INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A
# PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
# HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF
# CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE
# OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
#

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  puts "---------------------------------"
  puts "Ansible Sandbox"
  puts "---------------------------------"
  puts ""

  config.ssh.forward_x11 = true
  config.ssh.insert_key = false
  config.ssh.keep_alive = true

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
  #config.vm.provision :shell, path: "scripts/install_git.sh"
  #config.vm.provision :shell, path: "scripts/install_java.sh"
  #config.vm.provision :shell, path: "scripts/install_gradle.sh", args: ['3.5']
  #config.vm.provision :shell, path: "scripts/install_aws.sh"
  #config.vm.provision :shell, path: "scripts/install_node.sh"

  Dir.glob('vagrant/*.json') do |file|
    json = (JSON.parse(File.read(file)))['server']
    id = json.has_key?("id") ? json["id"] : "vagrant"
    network = json.has_key?("network") ? json["network"] : {}
    hostname = json.has_key?("hostname") ? json["hostname"] : "vagrant"
    hosts = json.has_key?("hosts") ? json["hosts"] : {}
    memory = json.has_key?("memory") ? json["memory"] : 512
    cpus = json.has_key?("cpus") ? json["cpus"] : 1
    syncproject = json.has_key?("syncproject") ? json["syncproject"] : false

    config.vm.define id do |server|
      server.vm.box = json.has_key?('box') ? json['box'] : "kkdt/c7dev"
      server.vm.hostname = hostname
      server.vm.define id

      if Vagrant.has_plugin?("vagrant-vbguest") then
        server.vbguest.auto_update = false
      end

      server.vm.provider "virtualbox" do |vb|
        vb.gui = false
        vb.name = id
        vb.memory = memory
        vb.cpus = cpus
      end
      
      if syncproject then
        server.vm.synced_folder ".", "/vagrant"
      end

      if hosts.has_key?("file") && hosts.has_key?("destination") && File.exist?("#{hosts['file']}")
        server.vm.provision "file", source: hosts['file'], destination: hosts['destination']
      end

      # contains a list of possible bridge adapters and the first one to successfully
      # bridged will be used
      bridge = network.has_key?("bridge") ? true : false
      if bridge then
        server.vm.network network['type'], ip: network['ip'], bridge: network['bridge']
      elsif network.has_key?("type") && network.has_key?("ip")
        server.vm.network network['type'], ip: network['ip']
      end

      if network.has_key?("ports") then
          network['ports'].each do |p|
              server.vm.network "forwarded_port", guest: p['guest'], host: p['host']
          end
      end

      server.vm.provision "ssh",  type: "shell", args: [ "#{hostname}", network['ip'] ], inline: <<-SHELL
        touch ~vagrant/.ssh/known_hosts
        ssh-keyscan -H $1 >> ~vagrant/.ssh/known_hosts
        ssh-keyscan -H $2 >> ~vagrant/.ssh/known_hosts

        mkdir -p ~vagrant/.ssh
        cp /vagrant/vagrant/id_rsa* ~vagrant/.ssh/

        chmod 700 ~vagrant/.ssh
        chmod 644 ~vagrant/.ssh/id_rsa.pub
        chmod 644 ~vagrant/.ssh/known_hosts
        chmod 600 ~vagrant/.ssh/id_rsa
        chown vagrant:vagrant -R ~vagrant/.ssh
        cat ~vagrant/.ssh/id_rsa.pub >> ~vagrant/.ssh/authorized_keys
      SHELL

      server.vm.provision "hosts", type: "shell", args: [ hostname ], inline: <<-SHELL
        if [ -f "/tmp/hosts.${1}" ]; then
           grep -v "${1}" "/tmp/hosts.${1}" >> /etc/hosts
        else
           echo "/tmp/hosts.${1} not found"
        fi
      SHELL

      server.vm.provision "trust", run: "never", type: "shell", args: [ ], inline: <<-SHELL
        if [ -f "/tmp/hosts.${HOSTNAME}" ]; then
           ips=$(cat "/tmp/hosts.${HOSTNAME}" | cut -d " " -f1 | grep -v "${HOSTNAME}")
           for t in ${ips}
           do
             ssh-keyscan -H $t >> ~vagrant/.ssh/known_hosts
           done

           hosts=$(cat "/tmp/hosts.${HOSTNAME}" | cut -d " " -f2 | grep -v "${HOSTNAME}")
           for t in ${hosts}
           do
             ssh-keyscan -H $t >> ~vagrant/.ssh/known_hosts
           done
        else
           echo "/tmp/hosts.${1} not found"
        fi
      SHELL

      server.vm.provision "ansible", run: "never", type: "shell", args: [ ], inline: <<-SHELL
        yum install -y centos-release-ansible-29
        yum install -y ansible
      SHELL

      server.vm.provision "docker", run: "never", type: "shell", args: [ network['ip'], 2367 ], inline: <<-SHELL
        # install docker
        rpm --import https://download.docker.com/linux/centos/gpg
        sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
        sudo yum install -y docker-ce docker-ce-cli containerd.io

        # configure the docker group for other users to run docker
        sudo groupadd docker
        sudo usermod -aG docker vagrant

        # specific configurations, i.e different daemon port
        mkdir -p /etc/docker
        sed -e "s/ZZZZZZZZZZZdockeripZZZZZZZZZZZ/${1}/g" -e "s/ZZZZZZZZZZZdockerportZZZZZZZZZZZ/${2}/g" /vagrant/vagrant/etc/docker-daemon.json > /etc/docker/daemon.json
        mkdir -p /opt/docker/data
        chown -R :docker /opt/docker
        chmod -R 770 /opt/docker

        # boot on startup
        sudo systemctl enable containerd.service
        sudo systemctl enable docker.service
        sudo systemctl start containerd.service
        sudo systemctl start docker.service
      SHELL

    end
  end

end
