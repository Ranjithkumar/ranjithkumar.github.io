---
author: ranjithru
comments: true
date: 2012-07-21 09:51:34+00:00
layout: post
slug: mysql-master-slave-replication
title: MySQL Master-Slave Replication
wordpress_id: 91
categories:
- Database
tags:
- database
- MySQL
---



**The advantages of replication:-**
1) Offload some queries from one server to other.
2) Use master for all writes and Use slave for all reads.

**Some basic stuff to remember before we go ahead:-**
1. Master and slave installations will be on different server instances.
2. The master should not be in use during the installation process (if master is already present).

**1) Setup Master server:-**

    
       install MySQL Server
       sudo apt-get install mysql-server


after installation, Configure it to make this as Master server.

Edit

    
       /etc/mysql/my.cnf


MySQL should listen to all IP Addresses, so we comment out the following lines:

    
       #skip-networking
       #bind-address = 127.0.0.1


Set unique server ID

    
       server-id=1


Enable binary logging

    
       log-bin = /var/log/mysql/mysql-bin.log


Restart MySQL by using the command

    
       sudo service mysql restart


Log in to the MySQL shell

    
       mysql -u root -p


**Create a replication user:**
Its recommended to create a separate user for mysql replication to which slaves can authenticate.  Slaves will be connecting to the master using this user’s credentials.

    
       GRANT REPLICATION SLAVE ON *.* TO 'slaveuser'@'%' IDENTIFIED BY   
     '<a_real_password>';
       FLUSH PRIVILEGES;
       FLUSH TABLES WITH READ LOCK;
       SHOW MASTER STATUS;


After running the above command, you should be able to see binary log position

    
      +------------------+----------+--------------+------------------+
       | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
       +------------------+----------+--------------+------------------+
       | mysql-bin.000001 |      107 |              |                  |
       +------------------+----------+--------------+------------------+


Write down the position, this would be needed later.

Note: If you already have a master setup with data, dump the data so that it can be imported to the slave for the data to be in sync.

Leave the shell.

    
       quit;


**2) Setup Slave server:-**

    
       install MySQL Server
       sudo apt-get install mysql-server


after installation, Configure it to make this as slave server.

Edit

    
       /etc/mysql/my.cnf


Set unique server ID

    
       Server-id=2


Restart MySQL by using the command

    
       sudo service mysql restart


Use below command to load the initial data from master

    
       mysql -u root -p<password> database_name < /path/to/masterdump.sql


Log in to the MySQL shell

    
       mysql -u root -p


We need to inform our slave server the details of master server like host name, replication username and password, etc. Other things that slave server need is master log file name and log position, which we have obtained by entering show master status on master server. Now we can connect slave with the master by issuing the following command

    
       CHANGE MASTER TO MASTER_HOST = '<host_name>', MASTER_USER ='slaveuser', 
     MASTER_PASSWORD='<a_real_password>', MASTER_LOG_FILE = 'mysql-bin.000001', 
    MASTER_LOG_POS =107;


Finally, start the slave

    
       START SLAVE;
       SHOW SLAVE STATUS\G; 
       quit;


Now in the master host run the following command to release the lock

    
       mysql> UNLOCK TABLES;


And now, each write to the master gets instantly replicated on the slave as well.


