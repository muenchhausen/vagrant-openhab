# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "public_network", ip: "192.168.10.20"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true
 
    # Customize the amount of memory on the VM:
    vb.memory = "1024"
  end
#  config.vm.provider "vmware_fusion" do |v|
#    # v.gui = true
#    v.vmx["memsize"] = "1024"
#    v.vmx["numvcpus"] = "2"
#  end

  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get -y update
    # sudo apt-get -y upgrade
    sudo apt-get -y install unzip supervisor wget
    echo "step1 done: apt-get"
    
    wget -nv --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" -O /tmp/jdk-7u67-linux-x64.tar.gz http://download.oracle.com/otn-pub/java/jdk/7u67-b01/jdk-7u67-linux-x64.tar.gz
    sudo tar -zxC /opt -f /tmp/jdk-7u67-linux-x64.tar.gz
    sudo ln -s /opt/jdk1.7.0_67 /opt/jdk7
    echo "step2 done: java install"
    
    wget -nv -O /tmp/distribution-1.6.1-runtime.zip https://github.com/openhab/openhab/releases/download/v1.6.1/distribution-1.6.1-runtime.zip
    wget -nv -O /tmp/distribution-1.6.1-addons.zip https://github.com/openhab/openhab/releases/download/v1.6.1/distribution-1.6.1-addons.zip
    wget -nv -O /tmp/distribution-1.6.1-greent.zip https://github.com/openhab/openhab/releases/download/v1.6.1/distribution-1.6.1-greent.zip
    echo "step3 done: wget openhab"    

    sudo mkdir -p /opt/openhab/addons-avail
    sudo unzip -d /opt/openhab /tmp/distribution-1.6.1-runtime.zip
    sudo unzip -d /opt/openhab/addons-avail /tmp/distribution-1.6.1-addons.zip
    sudo unzip -d /opt/openhab/webapps /tmp/distribution-1.6.1-greent.zip
    sudo chmod +x /opt/openhab/start.sh
    sudo mkdir -p /opt/openhab/logs
    echo "step4 done: openhab unzip"

    sudo cp /vagrant/data/files/supervisord.conf /etc/supervisor/supervisord.conf
    sudo cp /vagrant/data/files/openhab.conf /etc/supervisor/conf.d/openhab.conf
    sudo cp /vagrant/data/files/boot.sh /usr/local/bin/boot.sh
    sudo chmod +x /usr/local/bin/boot.sh
    echo "step5 done: supervisor"

    # individual configuration
    sudo cp -r /vagrant/data/openhab/configurations /opt/openhab
    sudo ln -s /opt/openhab/addons-avail/org.openhab.binding.knx-1.6.1.jar /opt/openhab/addons/
    sudo ln -s /opt/openhab/addons-avail/org.openhab.binding.ntp-1.6.1.jar /opt/openhab/addons/
    sudo ln -s /opt/openhab/addons-avail/org.openhab.persistence.rrd4j-1.6.1.jar /opt/openhab/addons/
    sudo ln -s /opt/openhab/addons-avail/org.openhab.io.multimedia.tts.freetts-1.6.1.jar /opt/openhab/addons/
    # sudo ln -s /opt/openhab/addons-avail/org.openhab.binding.http-1.6.1.jar /opt/openhab/addons/

    sudo cp /vagrant/data/files/openhab-restart /etc/network/if-up.d/openhab-restart
    sudo chmod +x /etc/network/if-up.d/openhab-restart
    echo "step6 done: openhab config"

    sudo /etc/init.d/supervisor restart &
    echo "step7 done: supervisor restart"

    sudo rm -rf /tmp/*
  SHELL

end
