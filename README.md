# CentOS Recipes
Repository for learning linux in general and CentOS specifics. This repository contains basically a whole bunch of small recipes :)
 
 - How do I know for how many hours my system is running?
 - How do I run scripts after reboot?
 - How do I install PHP 5.4?
 - How do I configure a static IP address?
 - How do I install Poppler PDF Utilities?
 - How do I get OS version?
 - How do I know how many cores my system has?
 - How do I install MySql?
 - How do I install Node.js?
 - How do I install Git?
 - How to allow specific user run command as sudo without password?
 - How do I install nginx?

### How do I know for how many hours my system is running? 

Besides running `uptime` you can run the below script if you need the result as a single number. Result is in hours.

```bash
echo "scale=2; `cat /proc/uptime | awk '{print $1}'` / 3600" | bc -l
```

### How do I run scripts after reboot?

There are several ways, the one I am used to is by using cron:

Log in with root account, run `crontab -e` then add any commands preceded by `@reboot`, for example:

```shell
@reboot echo "this is a test" > test.txt
```

The previous example will output a string to a text file. Keep in mind that your script will execute as root, what might be undesirable in many situations (like starting up application servers). In this case you can always use the `su` like the following:

```shell
@reboot su - user -c "pm2 start index.js" > startup.log 2>&1
```

### How do I install PHP 5.4?

**For Amazon Linux running on EC2**

Run `sudo yum install httpd24 php54`. If you want to upgrade from a previous version first you should remove the packages listed in `sudo yum list installed | grep "php"`, then remove httpd `sudo yum remove httpd*` and finally run `sudo yum install httpd24 php54`.

Source: http://superuser.com/questions/880522/how-to-yum-update-php-to-version-5-4-on-amazon-linux

**For other distros**

```shell
yum install php
yum install php-xml
```

### How do I configure a static IP address?

```plain
## Configure eth0
#
# vi /etc/sysconfig/network-scripts/ifcfg-eth0
 
DEVICE="eth0"
NM_CONTROLLED="yes"
ONBOOT=yes
HWADDR=A4:BA:DB:37:F1:04
TYPE=Ethernet
BOOTPROTO=static
NAME="System eth0"
UUID=5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03
IPADDR=192.168.1.44
NETMASK=255.255.255.0
 
 
## Configure Default Gateway
#
# vi /etc/sysconfig/network
 
NETWORKING=yes
HOSTNAME=centos6
GATEWAY=192.168.1.1
 
 
## Restart Network Interface
#
 
/etc/init.d/network restart
 
## Configure DNS Server
#
# vi /etc/resolv.conf
#
# or run `dhclient`
#
 
nameserver 8.8.8.8      # Replace with your nameserver ip
nameserver 192.168.1.1  # Replace with your nameserver ip
```

### How do I install Poppler PDF Utilities?
Just run `sudo yum install poppler-utils.x86_64`. This will allow you to use to following commands:
```
pdfdetach    pdffonts     pdfimages    pdfinfo      pdfroff      
pdfseparate  pdftocairo   pdftohtml    pdftoppm     pdftops      
pdftotext    pdfunite
```

### How do I get OS version?

You should run `cat /etc/centos-release`

### How do I know how many cores my system has?

Run the command `cat /proc/cpuinfo` to list details for all cores, and run `grep 'model name' /proc/cpuinfo | wc -l` to know how many cores your system has.

### How do I install MySql?

```bash
sudo yum upgrade -y
sudo yum install mysql -y
sudo yum install mysql-server -y
sudo /etc/init.d/mysqld start
mysqladmin -u root password 'rootPassword'
sudo chkconfig mysqld on
 
# my.cnf should be located at; /etc/my.cnf
# add the following line to allow external connections; 
# bind-address = 0.0.0.0
# then restart mysql to reload configurations; 
# sudo /etc/init.d/mysqld restart
```

You will be required to grant remote access to any user you would like to be able to connect from outside localhost.

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'rootPassword';
FLUSH PRIVILEGES;
```

This is a per database permission so don't forget to use wildcards or specify specific databases.

### How do I install Node.js?

```bash
curl https://raw.githubusercontent.com/creationix/nvm/v0.13.1/install.sh | bash
source ~/.bashrc
nvm install 0.10
nvm use 0.10
sudo visudo # add `dirname $(which node)` to secure_path so you're able to `sudo node` and `sudo npm`
```

### How do I install Git?

Simply run `sudo yum install -y git`

### How to allow specific user run command as sudo without password?

Log in as root then run `sudo visudo` and add the following line `joe ALL=(ALL) NOPASSWD: /full/path/to/command`, or if you want to restrict wich arguments this user may pass you can change that line to `joe ALL=(ALL) NOPASSWD: /full/path/to/command ARG1 ARG2`.

### How do I install nginx?

To install nginx you must run these commands:

```bash
sudo yum install epel-release
sudo yum install nginx
```

To start the services run `sudo /etc/init.d/nginx start`

If you happen to get errors after rebooting (e.g. `tail -f /var/log/nginx/error.log` shows something like `connect() to 127.0.0.1:7070 failed (13: Permission denied) while connecting to upstream`) you will have to follow [these steps](http://stackoverflow.com/a/24830777/91403): 

```shell
sudo cat /var/log/audit/audit.log | grep nginx | grep denied | audit2allow -M mynginx
sudo semodule -i mynginx.pp
```

If you don't have `audit2allow` and `semodule` binaries you will have to install them with `yum install policycoreutils-python`.
