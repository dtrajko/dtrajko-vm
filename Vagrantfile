# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

VM_HOSTNAME = "dtrajko-vm"
VM_IP = "192.168.56.20"
VM_GUI_ENABLED = true

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define VM_HOSTNAME do |dtrajko|
    dtrajko.vm.box = "fedora/26-cloud-base"
    dtrajko.vm.hostname = VM_HOSTNAME
    dtrajko.vm.network :private_network, ip: VM_IP
    # dtrajko.vm.box_check_update = false
  end

  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  # config.vm.synced_folder "/shared/folder/host", "/home/dtrajko/workspace", owner: "dtrajko", group: "dtrajko", mount_options: ["uid=1004", "gid=1004", "dmode=775", "fmode=664"], type: "virtualbox"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 3072
    vb.cpus = 2
    vb.name = VM_HOSTNAME
    vb.gui = VM_GUI_ENABLED
    # vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"] # fix for "Error: Failed to synchronize cache for repo 'updates"
  end

  # Run Ansible from the Vagrant VM
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "provisioning/playbook.yml"
    ansible.become = true # previously ansible.sudo = true
    ansible.verbose = "v"
    ansible.extra_vars = {
      vm_hostname: VM_HOSTNAME,
      vm_gui_enabled: VM_GUI_ENABLED
    }
  end

  # install Docker
  config.vm.provision "docker" do |d|
    # todo
  end

end
