# 🚀 WordPress Setup with Vagrant

## 📌 Overview
This project sets up a **WordPress** environment using **Vagrant** and **VirtualBox**. The configuration provisions an Ubuntu virtual machine and installs Apache, MySQL, PHP, and WordPress automatically.


## Prerequisites
Before using this Vagrant configuration, ensure you have the following installed:

## 🛠️ Prerequisites
Before starting, ensure you have the following installed:
- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)


## 📥 Getting Started

Follow these steps to set up the environment:

## Setup Instructions

### 1️⃣ Initialize Vagrant
Run the following command to initialize a new Vagrant environment:
```bash
vagrant init
```

### 2️⃣ Start the Virtual Machine
Run the following command to start the VM:
```bash
vagrant up
```

### 3️⃣ Access the VM
```bash
vagrant ssh
```

### 4️⃣ Open WordPress in Browser
Once the setup is complete, open your browser and visit:
```
http://192.168.56.98
```

## Configuration Details

- **Base Image:** Ubuntu 22.04 (Jammy Jellyfish)
- **Private Network IP:** `192.168.56.98`
- **Memory Allocation:** 1600MB
- **Provisioned Services:**
  - Apache Web Server
  - MySQL Database Server
  - PHP and required extensions
  - WordPress installation with pre-configured database

## Files Included

### 📜 Vagrantfile
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.network "private_network", ip: "192.168.56.98"
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1600"
    vb.gui = false
  end
  
  config.ssh.insert_key = false
  config.ssh.private_key_path = "~/.vagrant.d/insecure_private_key"
  
  config.vm.provision "shell", inline: <<-SHELL
  sudo apt update
  sudo apt install apache2 \
                   ghostscript \
                   libapache2-mod-php \
                   mysql-server \
                   php \
                   php-bcmath \
                   php-curl \
                   php-imagick \
                   php-intl \
                   php-json \
                   php-mbstring \
                   php-mysql \
                   php-xml \
                   php-zip -y
           
  sudo mkdir -p /srv/www
  sudo chown www-data: /srv/www
  curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www
  
  cat > /etc/apache2/sites-available/wordpress.conf <<EOF
<VirtualHost *:80>
      DocumentRoot /srv/www/wordpress
      <Directory /srv/www/wordpress>
          Options FollowSymLinks
          AllowOverride Limit Options FileInfo
          DirectoryIndex index.php
          Require all granted
      </Directory>
      <Directory /srv/www/wordpress/wp-content>
          Options FollowSymLinks
          Require all granted
      </Directory>
</VirtualHost>
EOF
  
  sudo a2ensite wordpress
  sudo a2enmod rewrite
  sudo a2dissite 000-default
  
  mysql -u root -e 'CREATE DATABASE wordpress;'
  mysql -u root -e 'CREATE USER wordpress@localhost IDENTIFIED BY "admin123";'
  mysql -u root -e 'GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;'
  mysql -u root -e 'FLUSH PRIVILEGES;'
  
  sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php
  sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
  sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
  sudo -u www-data sed -i 's/password_here/admin123/' /srv/www/wordpress/wp-config.php
  
  systemctl restart mysql
  systemctl restart apache2
  SHELL
end
```

## Accessing WordPress
After setting up the VM, open a browser and go to `http://192.168.56.98` to complete the WordPress installation.

## Stopping the VM
To stop the virtual machine, run:
```sh
vagrant halt
```

## Destroying the VM
To remove the virtual machine completely, run:
```sh
vagrant destroy -f
```

## 📚 Conclusion
This setup provides a **quick and automated way** to deploy a WordPress environment using Vagrant and VirtualBox. You can modify the `Vagrantfile` to suit your project needs.

## 👨‍🏫 Instructor
This project was guided by **Imran Teli**, who provided valuable mentorship throughout the process.
