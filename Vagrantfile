# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

VM_IP = '192.168.56.30'
VM_GUI_ENABLED = true
VM_OWNER_USERNAME = 'dtrajko'
VM_OWNER_GROUP = 'dtrajko'
VM_OWNER_PASSWORD = 'dtrajko'
VM_HOSTNAME = VM_OWNER_USERNAME + '-vm'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define VM_HOSTNAME do |host|
    host.vm.box = 'fedora/26-cloud-base'
    host.vm.hostname = VM_HOSTNAME
    host.vm.network :private_network, ip: VM_IP
    # host.vm.box_check_update = false
  end

  config.vm.synced_folder '.', '/vagrant', type: 'virtualbox'
  # config.vm.synced_folder 'D:\workspace\test-vm', '/home/' + VM_OWNER_USERNAME + '/workspace', owner: VM_OWNER_USERNAME, group: VM_OWNER_GROUP, mount_options: ['uid=1004', 'gid=1004', 'dmode=775', 'fmode=664'], type: 'virtualbox'

  config.vm.provider 'virtualbox' do |vb|
    vb.memory = 2048
    vb.cpus = 2
    vb.name = VM_HOSTNAME
    vb.gui = VM_GUI_ENABLED
    vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on'] # fix for 'Error: Failed to synchronize cache for repo 'updates'
  end

  # Run Ansible from the Vagrant VM
  config.vm.provision 'ansible_local' do |ansible|
    ansible.playbook = 'provisioning/playbook.yml'
    ansible.become = true # previously ansible.sudo = true
    ansible.verbose = 'v'
    ansible.extra_vars = {
      vm_hostname: VM_HOSTNAME,
      vm_gui_enabled: VM_GUI_ENABLED,
      vm_owner_username: VM_OWNER_USERNAME,
      vm_owner_group: VM_OWNER_GROUP,
      vm_owner_password: VM_OWNER_PASSWORD
    }
  end

  # install Docker
  config.vm.provision 'docker' do |d|
    # todo
  end

end
