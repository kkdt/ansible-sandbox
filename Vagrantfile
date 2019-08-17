# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

    puts "---------------------------------"
    puts "ansible-sandbox"
    puts "---------------------------------"

    config.ssh.forward_x11 = true
    config.ssh.insert_key = false
    config.ssh.keep_alive = true

    # Every Vagrant development environment requires a box. You can search for
    # boxes at https://vagrantcloud.com/search.
    config.vm.box = "kkdt/c7dev"

    Dir.glob('vagrant/*.json') do |file|
        json = (JSON.parse(File.read(file)))['server']
        id = json.has_key?("id") ? json["id"] : "ansible"
        network = hostname = json.has_key?("network") ? json["network"] : {}
        hostname = json.has_key?("hostname") ? json["hostname"] : "ansible"
        memory = json.has_key?("memory") ? json["memory"] : 512
        cpus = json.has_key?("cpus") ? json["cpus"] : 1
        hosts = json.has_key?("hosts") ? json["hosts"] : []

        config.vm.define id do |server|
            server.vm.hostname = hostname
            server.vm.define id

            server.vm.provider "virtualbox" do |vb|
                vb.name = id
                vb.memory = memory
                vb.cpus = cpus
            end

            server.vm.network network['type'], ip: network['ip']

            if network.has_key?("ports") then
                network['ports'].each do |p|
                    server.vm.network "forwarded_port", guest: p['guest'], host: p['host']
                end
            end

            hosts.each do |h|
                a = h["hostname"]
                ip = h["ip"]
                server.vm.provision "hosts", type: "shell", args: [ a, ip ], inline: <<-SHELL
                    echo "$2 $1 $1" >> /etc/hosts
                SHELL
            end

        end # end config.vm
    end # end Dir.glob

end