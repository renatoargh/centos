# centos
Repository for learning linux in general and CentOS/Amazon Linux specifics. This repository contains basically a whole bunch of small recipes :)

 - How do I know how many cores my system has?
 - How do I install MySql?

### How do I know how many cores my system has?

Run the command `cat /proc/cpuinfo` to list details for all cores, and run `grep 'model name' /proc/cpuinfo | wc -l` to know how many cores your system has.

### How do I install MySql?

```bash
sudo yum upgrade -y
sudo yum install mysql -y
sudo yum install mysql-server -y
sudo /etc/init.d/mysqld start
mysqladmin -u root password 'Senha'
sudo chkconfig mysqld on
 
# my.cnf should be located at; /etc/my.cnf
# add the following line to allow external connections; 
# bind-address = 0.0.0.0
# then restart mysql to reload configurations; 
# sudo /etc/init.d/mysqld restart
```

You will be required to grant remote access to any user you would like to be able to connect from outside localhost.

```sql
use mysql;
select host, user from user;
```

Choose which users to update then run another query similar to this:

```sql
update user set host="%" where user="root" and host="localhost.localdomain";
flush privileges;
```

