# Centos_Offline_Repo_Server
How to make a centos 7 offline repository 

====== This is a guide on how to make an Offline Repo Server =====


===== How to install apache on cenots 7 =====

<code>
yum install httpd -y

systemctl enable httpd.service

systemctl mask firewalld
</code>

Turn off SELINUX

<code>
vi /etc/selinux/config
</code>

Erase what is in the file and paste this

<code>

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
</code>

Then you need to move your repofiles into the apache server

<code>
 mv repo-drive /var/www/html/
</code>

you can now open firefox at 127.0.0.1/repo-drive and you will be able to see the apache server

===== Repofile Creation =====

Now we need to create a repofile for the other machines on the network that we want to be able to sync

First remove all the repos that are in the repofile

<code>
rm -rf /etc/yum.repos.d/*
</code>

You will now need to create a repofile

<code>
vi /etc/yum.repo.d/Offline-CentOS7.repo
</code>

Here is what you want to paste inside of the file

<code>
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
</code>


If you have an ipaddress set for your repo, you will need to put that into the host file.
In our case we are jsut sharing it to our local machine for now, so ad that entry into the host file like this.

<code>
vi /etc/hosts
</code>

and paste this below

<code>
#Add "rpmserver" to the localhost for now

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 rpmserver
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

#Uncomment the line below if want to place this machine in a network
#192.168.0.10   rpmserver

</code>


now you want to update your machine

<code>
yum clean all && yum repolist

yum update 
</code>


You should be all set!
