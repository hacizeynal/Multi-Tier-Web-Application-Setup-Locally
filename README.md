# Multi-Tier-Web-Application-Setup-Locally

## Introduction

Following technologies are used in this project

Nginx <br/>
Tomcat <br/>
RabbitMQ \
Memcached <br/>
MySQL <br/>

![](Screenshot%202022-10-03%20at%2022.23.41.png)

The request will be coming from a client browser and traffic will be redirected to the Nginx server, Nginx will be used as Load Balancer, it will forward requests to the Tomcat Application Server, Apache will be our server which our Java Application will be running. We can use shared storage as NFS. Our application server will forward traffic to the RabbitMQ which will be used as Message Broker. Message Broker will forward requests to the Memcached for Database caching which will cache our queries for MySQL.

We will use Vagrant to automate our VMs. It will communicate to the Oracle Virtualbox ,which is Hypervisor for VMs.
We will use Bash scripts to automate/configure our services such as Nginx ,Tomcat ,RabbitMQ ,MySQL and Memcached

## Prerequisites

#
- JDK 1.8 or later
- Maven 3 or later
- MySQL 5.6 or later

## Technologies 
- Spring MVC
- Spring Security
- Spring Data JPA
- Maven
- JSP
- MySQL
## Database
Here,we used Mysql DB 
MSQL DB Installation Steps for Linux ubuntu 14.04:
- $ sudo apt-get update
- $ sudo apt-get install mysql-server

Then look for the file :
- /src/main/resources/accountsdb
- accountsdb.sql file is a mysql dump file.we have to import this dump to mysql db server
- > mysql -u <user_name> -p accounts < accountsdb.sql

## MYSQL Setup

Login to the db vm
$ vagrant ssh db01
Verify Hosts entry, if entries missing update the it with IP and hostnames
```
cat /etc/hosts
```
Update OS with latest patches
```
yum update -y
```
Set Repository
```
yum install epel-release -y
```
Install Maria DB Package
```
yum install git mariadb-server -y
```
Starting & enabling mariadb-server
```
systemctl start mariadb
systemctl enable mariadb
```
RUN mysql secure installation script.
```
mysql_secure_installation
```
NOTE: Set db root password, I will be using admin123 as password

```
Set root password? [Y/n] Y New password:
Re-enter new password: Password updated successfully! Reloading privilege tables..
... Success!
By default, a MariaDB installation has an anonymous user, allowing anyone to log into MariaDB without having to have a user account created for them. This is intended only for testing, and to make the installation
go a bit smoother. You should remove them before moving into a production environment.
Remove anonymous users? [Y/n] Y ... Success!
Normally, root should only be allowed to connect from 'localhost'. This ensures that someone cannot guess at the root password from the network.
Disallow root login remotely? [Y/n] n ... skipping.
By default, MariaDB comes with a database named 'test' that anyone can access. This is also intended only for testing, and should be removed before moving into a production environment.
Remove test database and access to it? [Y/n] Y - Dropping test database...
... Success!
- Removing privileges on test database...
... Success!
Reloading the privilege tables will ensure that all changes made so far will take effect immediately.
Reload privilege tables now? [Y/n] Y ... Success!
```
Set DB name and users.
```
mysql -u root -padmin123
mysql> create database accounts;
mysql> grant all privileges on accounts.* TO 'admin'@’%’ identified by 'admin123' ; mysql> FLUSH PRIVILEGES;
mysql> exit;
```
Download Source code & Initialize Database.
```
git clone -b local-setup https://github.com/devopshydclub/vprofile-project.git # cd vprofile-project
mysql -u root -padmin123 accounts < src/main/resources/db_backup.sql
mysql -u root -padmin123 accounts
mysql> show tables;
```
Restart mariadb-server
```
systemctl restart mariadb
```
Starting the firewall and allowing the mariadb to access from port no. 3306
```
systemctl start firewalld
systemctl enable firewalld
firewall-cmd --get-active-zones
firewall-cmd --zone=public --add-port=3306/tcp --permanent # firewall-cmd --reload
systemctl restart mariadb
```
## MEMCACHE SETUP

Install, start & enable memcache on port 11211
```
yum install epel-release -y
yum install memcached -y
systemctl start memcached
systemctl enable memcached
systemctl status memcached
memcached -p 11211 -U 11111 -u memcached -d
```
Starting the firewall and allowing the port 11211 to access memcache
```
systemctl enable firewalld
systemctl start firewalld
systemctl status firewalld
firewall-cmd --add-port=11211/tcp --permanent # firewall-cmd --reload
memcached -p 11211 -U 11111 -u memcache -d
```
## RABBITMQ SETUP

Login to the RabbitMQ vm

```
vagrant ssh rmq01
```

Verify Hosts entry, if entries missing update the it with IP and hostnames
```
cat /etc/hosts
```
Update OS with latest patches
```
yum update -y
```
Set EPEL Repository
```
yum install epel-release -y
```
Install Dependencies
```
sudo yum install wget -y
```
```
cd /tmp/
wget http://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm #sudo rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
sudo yum -y install erlang socat
```

Install Rabbitmq Server
```
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash #sudo yum install rabbitmq-server -y
```
Start & Enable RabbitMQ
```
sudo systemctl start rabbitmq-server #sudo systemctl enable rabbitmq-server #sudo systemctl status rabbitmq-server
```
Config Change
```
sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
sudo rabbitmqctl add_user test test
sudo rabbitmqctl set_user_tags test administrator
```
Restart RabbitMQ service
```
systemctl restart rabbitmq-server
```
Enabling the firewall and allowing port 25672 to access the rabbitmq permanently
```
systemctl start firewalld
systemctl enable firewalld
firewall-cmd --get-active-zones
firewall-cmd --zone=public --add-port=25672/tcp --permanent # firewall-cmd --reload
```
