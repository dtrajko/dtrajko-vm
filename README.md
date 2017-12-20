# {{ VM_HOSTNAME }}
A web development virtual machine based on Vagrant / VirtualBox / Ansible


Dev VM
===================================



Requirements
-----------------------------------
- 64-bit host OS
- Vagrant
- Oracle VirtualBox


Basic info
-----------------------------------
Dev VM is created with:
- Vagrant 1.9.5                https://www.vagrantup.com/downloads.html
- Oracle VirtualBox v5.1.22    https://www.virtualbox.org/wiki/Downloads
- Ansible 2.3.1.0              running on guest OS only, installed by Vagrant ('ansible_local' method)


Configuration
-----------------------------------
Fedora 26
PHP 7.1.x
MariaDB 10.1.x
Nginx 1.11.3 (configured with nginx-push-stream-module)
Sphinx 2.2.11-id64-release (95ae9a6)
Subversion 1.9.6


Vagrant plugins
-----------------------------------
To avoid problems with shared folders, please install the following vagrant plugin
vagrant plugin install vagrant-hostsupdater
vagrant plugin install vagrant-vbguest


Administrator mode
-----------------------------------
To avoid problems with permissions during provisioning, it's recommended to run vagrant commands in a console with Administator / root permissions.


Running the Dev VM
-----------------------------------
- Install VirtualBox
- Install Vagrant
- Install Vagrant plugins (vagrant-hostsupdater, vagrant-vbguest)
- Edit Vagrantfile if default settings are not suitable
- Run 'vagrant up'
- Configure SSH for user '{{ VM_OWNER_USERNAME }}' (more defaults in the "SSH as user '{{ VM_OWNER_USERNAME }}'" section)
- SSH as user '{{ VM_OWNER_USERNAME }}'


User Accounts
-----------------------------------
Developer should use the following account to work in virtual machine:

username: '{{ VM_OWNER_USERNAME }}'
password: '{{ VM_OWNER_PASSWORD }}' (or use the ssh private key instead)

Vagrant user:
username: 'vagrant'
password: 'vagrant'

User 'vagrant' is not for a daily use, it's used for running vagrant commands, provisioning etc.

You can also use the root account, but ofcourse it not recommented. For
example, svn up as a root could make a mess with permissions for your .svn folders, 
so using root account is on developer's responsibillity.

username: 'root'
password: 'vagrant' (or use 'sudo su')


SSH as user '{{ VM_OWNER_USERNAME }}'
-----------------------------------
SSH as '{{ VM_OWNER_USERNAME }}' should be done without the password, with the private SSH key without the passphrase.
Public key already exists in VM for use '{{ VM_OWNER_USERNAME }}', while the private key can be found in provisioning/roles/users/files/ssh/id_rsa (OpenSSH RSA key)
The private key should be used in SSH client to login as '{{ VM_OWNER_USERNAME }}' without the password nor passphrase.
In PuTTY, try with the pre-generated provisioning/roles/users/files/ssh/id_rsa.ppk or use PuTTYgen to generate a new PuTTY private key based
on the existing OpenSSH provisioning/roles/users/files/ssh/id_rsa


SSH as user 'vagrant'
-----------------------------------
User 'vagrant' can login to VM with password 'vagrant'
User 'vagrant' should be used only for special purposes, it's not for a daily use.
For daily work on projects login as '{{ VM_OWNER_USERNAME }}'.


The codebase
-----------------------------------
By default, '/home/{{ VM_OWNER_USERNAME }}/workspace' is shared on host OS via samba on \\{{ VM_HOSTNAME }}\workspace
Alternatively, the code can be shared by using virtualbox shared folders in Vagrantfile:
config.vm.synced_folder '../../workspace', '/home/{{ VM_HOSTNAME }}/workspace',

Make sure that synced folder for the code is on a different path than "/vagrant" synced folder,
to avoid issues with svn and other problems.

Shared folder for the code in host OS should be available and empty.


Vagrant frequently used commands
-----------------------------------
vagrant up [--provision]     = Start and provision a VM. Force provisioning procedure with --provision.
vagrant ssh                  = ssh login to VM
vagrant status               = current status of the VM
vagrant halt                 = shut down the VM
vagrant destroy              = destroys the VM
vagrant reload [--provision] = vargant halt + vagrant up [--provision]
vagrant package              = packages a running vagrant environment into a box
vagrant help                 = even more options...


Default settings for Ansible
-----------------------------------
Default settings for the Ansible provisioning procedure can be edited in:
provisioning/roles/start/defaults/main.yml


GUI support
-----------------------------------

The virtual machine can be provisioned with or without the GUI support.
GUI can be enabled or disabled in Vagrantfile, by setting the variable VM_GUI_ENABLED.
GUI support is disabled by default:
If enabled, the provisioning script installs and enables the GNOME environment (default for Fedora).


Hosts
-----------------------------------
Add the following entries to hosts file:

192.168.XXX.XXX  phpmyadmin.localhost

192.168.XXX.XXX - IP address is the eth1 interface in VM (use ifconfig to find out which one)
It's the same as VM_IP variable in Vagrantfile.


MySQL server
-----------------------------------
host: 192.168.XXX.XXX (VM_IP variable in Vagrantfile)
port: 3306
user: '{{ VM_OWNER_USERNAME }}'
pass: '{{ VM_OWNER_PASSWORD }}'


phpMyAdmin
-----------------------------------
phpMyAdmin is available on following hosts:

phpmyadmin.localhost
phpmyadmin.{{ VM_HOSTNAME }}


Aliases
-----------------------------------
N/A (TODO)


Vagrant
-----------------------------------
Dev VM is prepared based on Vagrant specifications, so it can be exported to Vagrant box
vagrant package --base {{ VM_HOSTNAME }} --output {{ VM_HOSTNAME }}.box

and used through vagrant:
vagrant box add {{ VM_HOSTNAME }} /path/to/vagrant/boxes/{{ VM_HOSTNAME }}.box
vagrant init {{ VM_HOSTNAME }}
vagrant up

Dev VM contains two network adapters
Adapter #1: NAT
Adapter #2: Virtualbox Host-only Ethernet Adapter


Using the Dev VM
-----------------------------------
While developers are able to use their IDE on host OS to work with files, any mysql client
to work with data, and local svn client to commit or update - running local builds
should be done from within the VM, by using the console to start the build and
preview the results. This is because local build should use executables from VM -
php, ant etc.
