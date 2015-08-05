# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define :node1 do |node|
    node.vm.box = "ubuntu/vivid64"
    node.vm.network :private_network, ip: "10.200.19.40"
    node.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", 1024]
    end
  end

  config.vm.define :node2 do |node|
    node.vm.box = "ubuntu/trusty64"
    node.vm.network :private_network, ip: "10.200.19.41"
  end

  config.vm.define :node3 do |node|
    node.vm.box = "ubuntu/trusty64"
    node.vm.network :private_network, ip: "10.200.19.42"
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yml"

    ansible.groups = {
      "master" => ["node1"],
      "bind" => ["node2", "node3"],
    }

    ansible.extra_vars = "develop/group_vars/all"
  end

  config.ssh.insert_key = false
end
