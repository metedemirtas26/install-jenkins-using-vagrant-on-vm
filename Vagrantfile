# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"
  config.vm.hostname = "appsrv01"

  config.vm.network "private_network", ip: "192.168.33.10" # work only on this ip
  #config.vm.network "public_network" # if you want to show on your localhost, open this line
  #config.vm.network "forwarded_port", guest: 80, host: 8090

  config.vm.synced_folder "shared/", "/app/code"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 4096
    vb.cpus = 1
  end

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get -qq update && sudo apt-get -qq upgrade
    add-apt-repository ppa:openjdk-r/ppa -y
    apt-get update
    apt-get -y install apache2 openjdk-8-jdk
    usermod -a -G sudo vagrant
  SHELL

   # require plugin https://github.com/leighmcculloch/vagrant-docker-compose
  config.vagrant.plugins = "vagrant-docker-compose"

  # install docker and docker-compose
  config.vm.provision :docker
  config.vm.provision :docker_compose

  config.vm.provision "shell" do |s|
      s.path = 'sh/master.sh'
  end

  config.vm.provision "shell", inline: <<-SHELL
    sudo groupadd docker
    sudo usermod -aG docker ${USER}
    su -s ${USER}

    sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
    sudo chmod g+rwx "$HOME/.docker" -R

    usermod -a -G docker jenkins ${USER}

    sudo chmod 666 /var/run/docker.sock

    sudo systemctl restart docker
  SHELL

end
