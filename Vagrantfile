# -*- mode: ruby -*-
# vi: set ft=ruby :

# Sets guest environment variables.
# @see https://github.com/hashicorp/vagrant/issues/7015
$set_environment_variables = <<SCRIPT
tee "/etc/profile.d/myvars.sh" > "/dev/null" <<EOF
# Ansible environment variables.
export ANSIBLE_RETRY_FILES_ENABLED=0
export ANSIBLE_HOST_KEY_CHECKING=False
EOF
SCRIPT

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.ssh.private_key_path = "./insecure_private_key"
  config.vm.box = "ubuntu/bionic64"
  config.vm.define "mern16" do |machine|
    machine.vm.hostname = "mern16"
    machine.vm.network "private_network", ip: "192.168.4.16"
  end
  config.vm.define "controller15" do |machine|
    machine.vm.provision "shell", inline: $set_environment_variables, run: "always"
    machine.vm.network "private_network", ip: "192.168.4.15"
    machine.vm.synced_folder "~/.ansible", "/tmp/ansible"
    machine.vm.provision "ansible_local" do |ansible|
      ansible.install_mode = "pip"
      ansible.provisioning_path = "/vagrant/provision"
      ansible.playbook = "playbook.yml"
      ansible.become = true
      ansible.inventory_path = "hosts"
      ansible.limit = "all"
      ansible.vault_password_file = "/tmp/ansible/vaultpass"
      ansible.tags = ENV['ANSIBLE_TAGS']
      ansible.verbose = ENV['ANSIBLE_VERBOSE']
    end
  end
end
