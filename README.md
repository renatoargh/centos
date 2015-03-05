# centos
Repository for learning linux in general and CentOS/Amazon Linux specifics. This repository contains basically a whole bunch of small recipes :)

### How do I know how many cores my system has?

Run the command `cat /proc/cpuinfo` to list details for all cores, and run `grep 'model name' /proc/cpuinfo | wc -l` to know how many cores your system has.


