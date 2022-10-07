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
```cat /etc/hosts```
Update OS with latest patches
```yum update -y```
Set Repository
```yum install epel-release -y```
Install Maria DB Package
```yum install git mariadb-server -y```
Starting & enabling mariadb-server
```
systemctl start mariadb
systemctl enable mariadb
```
RUN mysql secure installation script.
```mysql_secure_installation```
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