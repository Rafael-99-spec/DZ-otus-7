# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.provision "shell", inline: <<-SHELL
  sudo yum install vim nano httpd -y
  sudo cp /vagrant/findword.service /etc/sysconfig/findword.service
  sudo cp /vagrant/findword.timer /etc/sysconfig/findword.timer
  sudo systemctl enable /etc/sysconfig/findword.service
  sudo systemctl enable /etc/sysconfig/findword.timer
  sudo systemctl start findword.service
  sudo systemctl start findword.timer
  SHELL
end
