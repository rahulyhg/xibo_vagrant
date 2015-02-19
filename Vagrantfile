# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  config.vm.define "server" do |server|
    server.vm.box = "ubuntu/trusty64"
    server.vm.network "private_network", ip: "192.168.1.10"

    server.vm.provision "shell", inline: <<-SHELL
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

     sudo rm /var/www/html/xibo/install.php
     sudo cp /vagrant/settings.php /var/www/html/xibo/
     sudo mysql -u $MyUSER -p$MyPASS $dbName < /vagrant/schema.sql
    SHELL
  end

  config.vm.define "client" do |client|
    client.vm.box = "ubuntu/precise32"
    client.vm.provider :virtualbox do |vb|
      vb.gui=true
      vb.name = "xibo_client"
    end
    client.vm.network "private_network", ip: "192.168.1.11"
    client.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get remove dictionaries-common
      sudo apt-get dist-upgrade -y
      sudo apt-get install -y dictionaries-common
      sudo apt-get install -y xfce4 virtualbox-ose-guest-dkms virtualbox-ose-guest-utils virtualbox-ose-guest-x11
      sudo VBoxClient-all
      #sudo apt-get install -y libboost-python1.40.0 libboost-thread1.40.0 libdc1394-22 libgraphicsmagick++3 libgraphicsmagick3 libavutil49 bzr python-soappy python-feedparser python-serial flashplugin-nonfree libavcodec52 libavformat52 libswscale0 libsdl1.2debian-pulseaudio libvdpau1

      sudo mkdir /opt/xibo
      #sudo tar -xvf /vagrant/libavg-1.7.1-vdpau-berkelium11.tar.gz -C /
      #sudo ldconfig
      #sudo apt-get install -y bzr python-imaging
      #sudo bzr branch lp:xibo/1.6 /opt/xibo/pyclient
    SHELL
  end
end
