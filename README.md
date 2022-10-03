# Multi-Tier-Web-Application-Setup-Locally

## Introduction

Following technologies are used in this project

Nginx <br/>
Tomcat <br/>
RabbitMQ \
Memcached <br/>
MySQL <br/>


![](https://file%2B.vscode-resource.vscode-cdn.net/var/folders/g8/lfx6dwx92nj708qvff6y55f00000gn/T/TemporaryItems/NSIRD_screencaptureui_l0TLjP/Screenshot%202022-10-03%20at%2022.21.49.png?version%3D1664828513396)

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
.
