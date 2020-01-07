# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/centos-7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

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
    vb.memory = "2048"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    yum update
    yum install -y yum-utils device-mapper-persistent-data lvm2
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    yum install -y git
    yum install -y libicu-devel
    yum install -y docker-ce
    systemctl enable docker
    systemctl start docker
    curl -L https://github.com/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose
    mkdir php
    echo "FROM php:7-apache" > php/Dockerfile
    echo "RUN apt-get update && apt-get install -y libzip-dev libicu-dev unzip zlib1g-dev && docker-php-ext-install pdo_mysql mysqli intl zip && a2enmod rewrite" >> php/Dockerfile
    echo "version: '3'" > docker-compose.yml
    echo "services:" >> docker-compose.yml
    echo "  web:" >> docker-compose.yml
    echo "    build: ./php" >> docker-compose.yml
    echo "    ports:" >> docker-compose.yml
    echo "      - '80:80'" >> docker-compose.yml
    echo "    volumes:" >> docker-compose.yml
    echo "      - /vagrant_data/html:/var/www/html" >> docker-compose.yml
    echo "    depends_on:" >> docker-compose.yml
    echo "      - db" >> docker-compose.yml
    echo "  db:" >> docker-compose.yml
    echo "    image: mysql" >> docker-compose.yml
    echo "    command: --default-authentication-plugin=mysql_native_password" >> docker-compose.yml
    echo "    volumes:" >> docker-compose.yml
    echo "      - db_data:/var/lib/mysql" >> docker-compose.yml
    echo "    restart: always" >> docker-compose.yml
    echo "    environment:" >> docker-compose.yml
    echo "      MYSQL_ROOT_PASSWORD: example" >> docker-compose.yml
    echo "    ports:" >> docker-compose.yml
    echo "      - 3306:3306" >> docker-compose.yml
    echo "volumes:" >> docker-compose.yml
    echo "  db_data: {}" >> docker-compose.yml
    /usr/local/bin/docker-compose up -d
  SHELL
end
