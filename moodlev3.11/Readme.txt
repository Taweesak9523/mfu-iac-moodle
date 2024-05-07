#### Pre-install Require!!! ####
#1 Create Key pair for ec2
#2 Create cluster parameter group Name"custom-mysql-aurora3-cluster" and cofig db "character_set_server" to "utf8mb4" and cofig db time_zone Asia/Bangkok
    - family=aurora-mysql8.0
    - name=custom-mysql-aurora3-cluster
    - character_set_server=utf8mb4
    - time_zone=Asia/Bangkok
#3 Create Instane parameter group Name"custom-mysql-aurora3-instance" and cofig parameter "max_connection" to "16000"
    - family=aurora-mysql8.0
    - name=custom-mysql-aurora3-instance
    - max_connection=16000
#4 Before install moodle edit config /var/www/moodledata/lib/dml/mysqli_native_moodle_database.php set parameter $this->compressedrowformatsupported = true; to "$this->compressedrowformatsupported = false;"
#5 Rester web server and php-fpm with command 
    sudo systemctl restart httpd && systemctl restart php-fpm

#optional install moodle with command

cd /var/www/moodledata
sudo -u apache /usr/bin/php admin/cli/install_database.php --adminuser=admin --adminpass='u@nyQxWZ8IXG' --adminemail='admin@local.com' --agree-license
#Note after install with command please wait for 2-3 minite after that set time zone moodel web to use "Asia/Bangkok" at Siteadministrator "Location Seting" 

#### Post-install moodel part ####
#1 Deploy session cache with update cloudformation template.
#2 Add redis application cache to moodle and test it.
#3 Set site-name (if install with command )
#4 Disable guset login
#5 Disable notification

#### Post-install aws part ####
#1 Create AMI image webcache 
#2 Convert Lunchconfig to lunchtemplate 
#3 Modify lunchtemplate version and use new AIM and config it to use new IAM, instance-type, Remove user-data then add new user-data 
    #!/bin/bash
    #Remove all localcache when lunch new instance. 
    sudo rm -rf /localcache/*
#4 Update ASG to use new lunchtemplate.

####### List rds aurora version #######
[cloudshell-user@ip-10-2-59-174 ~]$ aws rds describe-db-engine-versions --engine aurora-mysql --query "DBEngineVersions[].EngineVersion"
[
    "5.7.mysql_aurora.2.07.9",
    "5.7.mysql_aurora.2.07.10",
    "5.7.mysql_aurora.2.08.3",
    "5.7.mysql_aurora.2.11.1",
    "5.7.mysql_aurora.2.11.2",
    "5.7.mysql_aurora.2.11.3",
    "5.7.mysql_aurora.2.12.0",
    "8.0.mysql_aurora.3.01.0",
    "8.0.mysql_aurora.3.01.1",
    "8.0.mysql_aurora.3.02.0",
    "8.0.mysql_aurora.3.02.1",
    "8.0.mysql_aurora.3.02.2",
    "8.0.mysql_aurora.3.02.3",
    "8.0.mysql_aurora.3.03.0",
    "8.0.mysql_aurora.3.03.1",
    "8.0.mysql_aurora.3.03.2",
    "8.0.mysql_aurora.3.04.0"
]

### command monitor ###
iostat -xd nvme1n1 2  

sar 2  -o /tmp/report/cpu
sar -r 2 -o /tmp/report/memory
sar -n ALL /tmp/report/network

sudo amazon-linux-extras install epel
yum install nmon
export NMON=mndc

https://mfu-ica-demo-moodle-bucket.s3.ap-southeast-1.amazonaws.com