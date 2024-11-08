Sure, here's a README file for your GitHub repository:


# ERPNext Installation Guide

## Table of Contents
1. #server-setup
2. #install-required-packages
3. #configure-mysql-server
4. #install-curl-node-npm-yarn
5. #install-frappe-bench
6. #install-erpnext-and-other-apps
7. #setup-production-server

## PRE-REQUISITES
- **Operating System:** Linux Ubuntu 22.04 LTS
- **Minimum Recommended Hardware:** 2 CPU | 2 GB RAM | 10 GB Disk
- **Root shell access to the server (via SSH)**

## 1. Server Setup
### 1.1 Login to the server as root user
### 1.2 Setup correct date and timezone
Check the server’s current timezone:
```bash
date
```
Set correct timezone as per your region:
```bash
timedatectl set-timezone "Asia/Kolkata"
```
### 1.3 Update & upgrade server packages
```bash
sudo apt-get update -y
sudo apt-get upgrade -y
```
### 1.4 Create a new user
```bash
sudo adduser [frappe-user]
usermod -aG sudo [frappe-user]
su [frappe-user]
cd /home/[frappe-user]/
```
Note: Replace `[frappe-user]` with your username. Eg. `sudo adduser myname`

## 2. Install Required Packages
### 2.1 Install GIT
```bash
sudo apt-get install git
```
Check if GIT is correctly installed:
```bash
git --version
```
### 2.2 Install Python
```bash
sudo apt-get install python3-dev python3.10-dev python3-setuptools python3-pip python3-distutils
```
### 2.3 Install Python Virtual Environment
```bash
sudo apt-get install python3.10-venv
```
Check if Python is correctly installed:
```bash
python3 -V
```
### 2.4 Install Software Properties Common
```bash
sudo apt-get install software-properties-common
```
### 2.5 Install MariaDB (MySQL server)
```bash
sudo apt install mariadb-server mariadb-client
```
Check if MariaDB is correctly installed:
```bash
mariadb --version
```
### 2.6 Install Redis Server
```bash
sudo apt-get install redis-server
```
### 2.7 Install other necessary packages
```bash
sudo apt-get install xvfb libfontconfig wkhtmltopdf
sudo apt-get install libmysqlclient-dev
```

## 3. Configure MySQL Server
### 3.1 Setup the server
```bash
sudo mysql_secure_installation
```
Follow the instructions during the setup process:
- Enter current password for root: (Enter your SSH root user password)
- Switch to unix_socket authentication [Y/n]: Y
- Change the root password? [Y/n]: Y
- Remove anonymous users? [Y/n] Y
- Disallow root login remotely? [Y/n]: N
- Remove test database and access to it? [Y/n]: Y
- Reload privilege tables now? [Y/n]: Y

### 3.2 Edit the MySQL default config file
```bash
sudo vim /etc/mysql/my.cnf
```
Add the following code block at the bottom of the file:
```ini
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```
### 3.3 Restart the MySQL server
```bash
sudo service mysql restart
```

## 4. Install CURL, Node, NPM, Yarn
### 4.1 Install CURL
```bash
sudo apt install curl
```
### 4.2 Install Node
```bash
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
source ~/.profile
nvm install 16.15.0
```
### 4.3 Install NPM
```bash
sudo apt-get install npm
```
### 4.4 Install Yarn
```bash
sudo npm install -g yarn
```
Check if Node is correctly installed:
```bash
node --version
```

## 5. Install Frappe Bench
### 5.1 Install Frappe Bench
```bash
sudo pip3 install frappe-bench
```
Check if Frappe Bench is correctly installed:
```bash
bench --version
```
### 5.2 Initialize Frappe Bench
```bash
bench init --frappe-branch version-14 frappe-bench
```
### 5.3 Go to Frappe Bench directory
```bash
cd frappe-bench/
```
### 5.4 Change user directory permissions
```bash
chmod -R o+rx /home/[frappe-user]/
```
### 5.5 Create a New Site
```bash
bench new-site site1.local
```

## 6. Install ERPNext and other Apps
### 6.1 Download the necessary apps
```bash
bench get-app payments
bench get-app --branch version-14 erpnext
bench get-app hrms
bench get-app ecommerce_integrations --branch main
```
Check if all the apps are correctly downloaded:
```bash
bench version --format table
```
### 6.2 Install all the Apps
```bash
bench --site site1.local install-app erpnext
bench --site site1.local install-app hrms
bench --site site1.local install-app ecommerce_integrations
```

## 7. Setup Production Server
### 7.1 Enable scheduler service
```bash
bench --site site1.local enable-scheduler
```
### 7.2 Disable maintenance mode
```bash
bench --site site1.local set-maintenance-mode off
```
### 7.3 Setup production config
```bash
sudo bench setup production [frappe-user]
```
### 7.4 Setup NGINX web server
```bash
bench setup nginx
```
### 7.5 Final server setup
```bash
sudo supervisorctl restart all
sudo bench setup production [frappe-user]
```
When prompted to save new/existing config files, hit “Y”.

:tada: **Ready to Go!**
You can now go to your server `[IP-address]:80` and you will have a fresh new installation of ERPNext ready to be configured!

If you are facing any issues with the ports, make sure to enable all the necessary ports on your firewall:
```bash
sudo ufw allow 22,25,143,80,443,3306,3022,8000/tcp
sudo ufw enable
```
```

Feel free to customize this README file further to suit your needs!
