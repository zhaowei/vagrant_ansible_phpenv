# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"
#ssh  -o StrictHostKeyChecking=no 127.0.0.1
# check and install required Vagrant plugins
# vagrant plugin install vagrant-proxyconf --plugin-source http://rubygems.org
#required_plugins = ["vagrant-hostmanager", "vagrant-vbguest", "vagrant-cachier", "vagrant-unison"]
required_plugins = ["vagrant-hostmanager", "vagrant-cachier", "vagrant-unison"]
#required_plugins = []
required_plugins.each do |plugin|
	if Vagrant.has_plugin?(plugin) then
	    system "echo OK: #{plugin} already installed"
	else
	    system "echo Not installed required plugin: #{plugin} ..."
		system "vagrant plugin install #{plugin} --plugin-source http://rubygems.org"
	end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    # Every Vagrant virtual environment requires a box to build off of.
    # config.vm.box = "CentOS6.4"

    # The url from where the 'config.vm.box' box will be fetched if it
    # doesn't already exist on the user's system.
    config.vm.box = "centos64-x86_64-20140116.box"

    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    config.vm.network "private_network", ip: "192.168.88.88"

    # Create a public network, which generally matched to bridged network.
    # Bridged networks make the machine appear as another physical device on
    # your network.
    # config.vm.network "public_network"

    # If true, then any SSH connections made will enable agent forwarding.
    # Default value: false
    # config.ssh.forward_agent = true

    # Share an additional folder to the guest VM. The first argument is
    # the path on the host to the actual folder. The second argument is
    # the path on the guest to mount the folder. And the optional third
    # argument is a set of non-required options.
    # config.vm.synced_folder "./", "/var/www", type: "smb"
    # config.vm.synced_folder "./", "/var/www"

    config.sync.host_folder = "src/"  #relative to the folder your Vagrantfile is in
    config.sync.guest_folder = "src/" #relative to the vagrant home folder -> /home/vagrant
    config.sync.ignore = "Name {.idea,.DS_Store}"


    # Provider-specific configuration so you can fine-tune various
    # backing providers for Vagrant. These expose provider-specific options.
    # Example for VirtualBox:
    #
    config.vm.provider "virtualbox" do |vb|
        # Don't boot with headless mode
        # vb.gui = true
        # Use VBoxManage to customize the VM. For example to change memory:
        # vb.customize ["modifyvm", :id, "--memory", "1024"]
        vb.customize ["modifyvm", :id, "--name", "kcdns-dev"]
        # vb.customize ["modifyvm", :id, "--cpuexecutioncap", "90"]
        # By default set to 1, change it to amount of your CPUs
        # vb.customize ["modifyvm", :id, "--cpus", "2" ]
        # Or uncomment line above for Automatic set VirtualBox guest CPU count to the number of host cores
        # WARNING ! Works on Linux Host ONLY
        # vb.customize ["modifyvm", :id, "--cpus", `grep "^processor" /proc/cpuinfo | wc -l`.chomp ]
    end

     #Set entries in hosts file
     config.hostmanager.enabled = true
     config.hostmanager.manage_host = true
     config.hostmanager.ignore_private_ip = false
     config.hostmanager.include_offline = true
     config.hostmanager.aliases =  ["test.local", "phpmyadmin.local"]

    if Vagrant.has_plugin?("vagrant-cachier")
        config.cache.scope = :box
    end

    #
    # Pre-provisioning
    # Install Ansible on the VM to run main provisioning from the VM itself
    #
    $script = <<SCRIPT
        sudo yum install ansible -y
SCRIPT

    config.vm.provision "shell", inline: $script

    #
    # Run Ansible provisioning inside the VM
    #
    config.vm.provision "shell" do |sh|
        sh.inline = "ansible-playbook /vagrant/phpenv-nginx-ansible-vagrant/ansible/site.yml -i 'vagrant,' --connection=local"
    end
end
