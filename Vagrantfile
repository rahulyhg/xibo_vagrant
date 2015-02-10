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
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
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
     sudo apt-get update
     sudo apt-get install -y apache2
     sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password password rootpassword'
     sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password rootpassword'
     sudo apt-get install -y mysql-server
     sudo apt-get install -y php5 php5-mysql php5-gd php5-mcrypt
     sudo sed -i 's/^upload_max_fliesize.*$/upload_max_filesize = 128M/g' /etc/php5/apache2/php.ini
     sudo sed -i 's/^post_max_size.*$/post_max_size = 128M/g' /etc/php5/apache2/php.ini
     sudo sed -i 's/^max_execution_time.*$/max_execution_time = 120/g' /etc/php5/apache2/php.ini
     php5enmod mcrypt

     curl -sS -L https://github.com/xibosignage/xibo-cms/archive/1.7.1.tar.gz > ~/xibo-1.7.1-server.tar.gz

     cd /var/www
     sudo tar zxvf ~/xibo-1.7.1-server.tar.gz
     sudo mv xibo-cms-1.7.1 html/xibo
     sudo chown www-data.www-data -R html/xibo

     sudo mkdir /xibo-library
     sudo chown www-data.www-data -R /xibo-library
     sudo /etc/init.d/apache2 restart

     MyUSER="root"
     MyPASS="rootpassword"
     dbName="xibodatabasename"
     dbUser="xibodbusername"
     dbPass="xibodatabasepassword"
     HostName="localhost"
     mysql -u $MyUSER -h $HostName -p$MyPASS -Bse "CREATE DATABASE $dbName;"
     mysql -u $MyUSER -h $HostName -p$MyPASS -Bse "GRANT ALL ON $dbName.* to '$dbUser'@'$HostName' identified by '$dbPass';"
   SHELL
end
