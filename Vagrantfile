# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.provision "shell", inline: <<-SHELL
  sudo yum install vim nano epel-release -y && yum install spawn-fcgi php php-cli mod_fcgid httpd -y
  sudo cp /vagrant/findword /etc/sysconfig/findword
  sudo cp /vagrant/findword.service /etc/systemd/system/findword.service
  sudo cp /vagrant/findword.timer /etc/systemd/system/findword.timer
  sudo cp /vagrant/findword.log /var/log/findword.log
  sudo systemctl enable findword.service
  sudo systemctl enable findword.timer
  sudo systemctl start findword.service
  sudo systemctl start findword.timer
  sudo echo "DZ7-1 done!!!"
  sudo yum install epel-release -y
  sudo yum install spawn-fcgi php php-cli mod_fcgid httpd -y
  sudo echo "SOCKET=/var/run/php-fcgi.sock" > /etc/sysconfig/spawn-fcgi 
  sudo echo "OPTIONS="-u apache -g apache -s $SOCKET -S -M 0600 -C 32 -F 1 -P /var/run/spawn-fcgi.pid -- /usr/bin/php-cgi"" >> /etc/sysconfig/spawn-fcgi
  sudo cp /vagrant/spawn-fcgi.service /etc/systemd/system/spawn-fcgi.service
  sudo systemctl enable spawn-fcgi
  sudo systemctl start spawn-fcgi
  sudo echo "DZ7-2 done!!!"
  sudo cp /vagrant/httpd@.service /etc/systemd/system/httpd@.service
  sudo touch /etc/sysconfig/httpd-one
  sudo echo "OPTIONS=-f conf/one.conf" > /etc/sysconfig/httpd-one
  sudo echo "LANG=C" >> /etc/sysconfig/httpd-one
  sudo touch /etc/sysconfig/httpd-two
  sudo echo "OPTIONS=-f conf/two.conf" > /etc/sysconfig/httpd-two
  sudo echo "LANG=C" >> /etc/sysconfig/httpd-two
  sudo cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/one.conf
  sudo cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/two.conf
  sudo echo "PidFile /var/run/httpd-two.pid" >> /etc/httpd/conf/two.conf
  sudo sed -i "s/Listen 80/Listen 8080/g" /etc/httpd/conf/two.conf
  sudo systemctl start httpd@one httpd@two
  SHELL
end
