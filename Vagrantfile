# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/stream8"
  config.vm.provider "virtualbox" do |v|
    v.gui = true
    v.gui = false
    v.memory = 4096
    v.cpus = 4
  end
  config.vm.define "chef-server" do |chef_server|
    chef_server.vm.hostname = "chef-server"
    chef_server.vm.provision "shell", inline: <<-SHELL
      # Install Chef server
      sudo rpm -Uvh 'https://omnitruck.chef.io/stable/chef-server/download?p=el&pv=8&m=x86_64&prerelease=false&nightlies=false'
      sudo chef-server-ctl reconfigure --chef-license=accept
      # Generate a new organization
      sudo useradd -m chefadmin
      sudo chef-server-ctl org-create myorg "My Organization" --association_user chefadmin
    SHELL
  end
  config.vm.define "chef-client" do |chef_client|
    chef_client.vm.hostname = "chef-client"
    chef_client.vm.provision "shell", inline: <<-SHELL
      # Install Chef client
      sudo rpm -Uvh 'https://omnitruck.chef.io/stable/chef/download?p=el&pv=8&m=x86_64&prerelease=false&nightlies=false'
      # Download validation key
      wget https://chef-server/myorg-validator.pem
      sudo mv myorg-validator.pem /etc/chef/validation.pem
      # Bootstrap node to Chef server
      sudo chef-client -S https://chef-server/organizations/myorg -K /etc/chef/validation.pem
    SHELL
  end
end