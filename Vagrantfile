# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.network "forwarded_port", guest: 80, host: 8080

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

     sudo rm /var/www/html/xibo/install.php
     sudo cp /vagrant/settings.php /var/www/html/xibo/
     sudo mysql -u $MyUSER -p$MyPASS $dbName < /vagrant/schema.sql
  SHELL
end
