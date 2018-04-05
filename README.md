# Centos_Offline_Repo_Server
How to make a centos 7 offline repository 

====== This is a guide on how to make an Offline Repo Server =====


===== How to install apache on cenots 7 =====

```
yum install httpd -y

systemctl enable httpd.service

systemctl mask firewalld
```

Turn off SELINUX

```
vi /etc/selinux/config
```

Erase what is in the file and paste this

```

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=permissive
```

Then you need to move your repofiles into the apache server

```
 mv repo-drive /var/www/html/
```

you can now open firefox at 127.0.0.1/repo-drive and you will be able to see the apache server

===== Repofile Creation =====

Now we need to create a repofile for the other machines on the network that we want to be able to sync

First remove all the repos that are in the repofile

```
rm -rf /etc/yum.repos.d/*
```

You will now need to create a repofile

```
vi /etc/yum.repo.d/Offline-CentOS7.repo
```

Here is what you want to paste inside of the file

```
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS 7 Base
baseurl=http://rpmserver/repo-drive/centos7/base/
enabled=1
gpgcheck=0

[updates]
name=CentOS 7 Updates
baseurl=http://rpmserver/repo-drive/centos7/updates
enabled=1
gpgcheck=0

[extras]
name=CentOS 7 Extras
baseurl=http://rpmserver/repo-drive/centos7/extras
enabled=1
gpgcheck=0

[nux-dextop]
name=CentOS 7 Nux-Dextop
baseurl=http://rpmserver/repo-drive/centos7/nux-dextop
enabled=1
gpgcheck=0

[epel]
name=CentOS 7 epel
baseurl=http://rpmserver/repo-drive/centos7/epel/
enabled=1
gpgcheck=0

```


If you have an ipaddress set for your repo, you will need to put that into the host file.
In our case we are jsut sharing it to our local machine for now, so ad that entry into the host file like this.

```
vi /etc/hosts
```

and paste this below

```
#Add "rpmserver" to the localhost for now

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 rpmserver
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

#Uncomment the line below if want to place this machine in a network
#192.168.0.10   rpmserver

```


now you want to update your machine

```
yum clean all && yum repolist

yum update 
```


You should be all set!
