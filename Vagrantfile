# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  config.vm.define "control" do |control|
    control.vm.box = "hashicorp/bionic64"
    control.vm.hostname = 'control'
    control.vm.network "private_network", ip: "192.168.50.4" 
    control.vm.provision "file", source: "keys/ansible_key", destination: "~/.ssh/ansible_key"
    
    # <<- string converted into the shell. takes the stuff from shell
    control.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt -y install software-properties-common
      sudo apt-add-repository --yes --update ppa:ansible/ansible
      sudo apt -y install ansible
      chmod 600 /home/vagrant/.ssh/ansible_key
      echo "web ansible_connection=ssh ansible_host=192.168.50.5 ansible_port=22 ansible_ssh_private_key_file=/home/vagrant/.ssh/ansible_key ansible_user=vagrant" >> /etc/ansible/hosts 
      echo "db ansible_connection=ssh ansible_host=192.168.50.6 ansible_port=22 ansible_ssh_private_key_file=/home/vagrant/.ssh/ansible_key ansible_user=vagrant" >> /etc/ansible/hosts 
      echo "ansible -m ping all\n\ ansible-playbook /vagrant/tomcat.yml"
      SHELL
  end
  
  config.vm.define "web" do |web|
    web.vm.box = "centos/7"
    web.vm.hostname = 'web'
    web.vm.network "private_network", ip: "192.168.50.5" 
    web.vm.network "forwarded_port", id: "tomcat", guest: 8080, host: 8080
    
    web.vm.provision "shell", inline: <<-SHELL
      cat /vagrant/keys/ansible_key.pub>>/home/vagrant/.ssh/authorized_keys
    SHELL
  end

  config.vm.define "db" do |db|
     db.vm.box = "centos/7"
     db.vm.hostname = "db"
     db.vm.network "private_network", ip: "192.168.50.6"
     db.vm.provision "shell", inline: <<-SHELL
     cat /vagrant/keys/ansible_key.pub>>/home/vagrant/.ssh/authorized_keys
   SHELL
  end

end
