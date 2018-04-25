#### Overview:
###### Nagios client machines are monitored by the plugin ‘check_by_ssh’ where supported, as it offers increased security over Nagios Remote Plugin Executor (NRPE). ‘check_by_ssh’ relies on the client machine’s SSH daemon with data encryption using public key authentication. Whereas NRPE requires an additional daemon to be installed and only offers hostname/IP Address matching for authentication which can be spoofed.

The process to configure the client machines is fairly simple. However, the implementation will vary slightly by OS and architecture. Key requirements:
-	Local user named ‘nagios’
-	SSH public key authentication (authorized_keys)
-	Nagios plugins 

Additional steps are required for systems that block ICMP requests, those that are behind a Firewall or are NAT’ed.


#### Client Configuration: 
###### Only those operating system and architecture combinations that we have setup before are listed below.
_Run commands as root or with sudo_
#### Ubuntu 14/16 - x86
``` bash
apt-get update && apt-get -y install nagios-plugins-common nagios-plugins gcc perl fping qstat
mkdir -p /usr/local/nagios/ && ln -s /usr/lib/nagios/plugins /usr/local/nagios/libexec
# Setup nagios user account
adduser nagios --shell /bin/bash --disabled-password --gecos ""
su - nagios
install -d -m 0700 ~/.ssh && touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLCl3cMafCiSK76iiT3kktL55QN/Cx2Ub0HT8wnJtw0bNn00KP+k+2u7DLF8OSI6v8MESgXIy6EDaSS7I+/sGV8xn0GLWWTSU8eUJ9DK5U0MWuvYM7kBl2z8s9VcW2rnZziSJcdBvgT60TWS3QEAZG78XM2E/h3iTR1UpbaCcECnsztbMyc9iwySLuN8u1p+D/KFlfvi8jm5Jn87WKH8PbEO9KU45PkukdQ14xk7h4xzDmWHc7cZweK8NvpSmLu5Ql0OB8bdmU6n876r0oNa2BvGucQZvkdd4UoaWkLDQ/4EPqQUfPvAJxbmXeQDo8SzE6J/a283aU92McNVwpcTVF nagios@Ubuntu-1604-xenial-64-minimal" >> ~/.ssh/authorized_keys
exit
# Install addon plugin for check_mem
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/justintime/nagios-plugins/master/check_mem/check_mem.pl && mv check_mem.pl check_mem && chmod +x check_mem
# All plugins will be installed here: /usr/lib/nagios/plugins with symlink to: /usr/local/nagios/libexec/
```
#### CentOS 7, RHEL 6, RHEL 7, Fedora 24, OpenSuSE 42, SLES 12 - x86 & s390 & ppc64le
``` bash
# CentOS 7: 
yum -y install epel-release
# RHEL 7, Fedora 24: 
yum -y install http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-9.noarch.rpm
# RHEL 6: 
yum -y install http://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
# OpenSuSE 42, SLES 12:
zypper refresh && zypper install gcc make perl fping bind-utils

# All expect OpenSuSE & SLES
yum -y install nagios-common nagios-plugins gcc glibc glibc-common openssl-devel perl libdbi fping qstat bind-utils

# Setup nagios user account
chsh -s /bin/bash nagios
su - nagios
install -d -m 0700 ~/.ssh && touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLCl3cMafCiSK76iiT3kktL55QN/Cx2Ub0HT8wnJtw0bNn00KP+k+2u7DLF8OSI6v8MESgXIy6EDaSS7I+/sGV8xn0GLWWTSU8eUJ9DK5U0MWuvYM7kBl2z8s9VcW2rnZziSJcdBvgT60TWS3QEAZG78XM2E/h3iTR1UpbaCcECnsztbMyc9iwySLuN8u1p+D/KFlfvi8jm5Jn87WKH8PbEO9KU45PkukdQ14xk7h4xzDmWHc7cZweK8NvpSmLu5Ql0OB8bdmU6n876r0oNa2BvGucQZvkdd4UoaWkLDQ/4EPqQUfPvAJxbmXeQDo8SzE6J/a283aU92McNVwpcTVF nagios@Ubuntu-1604-xenial-64-minimal" >> ~/.ssh/authorized_keys
exit
# return to root user
# Downloading and compile the plugin tools
cd /tmp && wget https://nagios-plugins.org/download/nagios-plugins-2.2.1.tar.gz
tar -zxvf nagios-plugins-2.2.1.tar.gz && cd nagios-plugins-2.2.1/
./configure --prefix=/usr/local/nagios
# For ppc64le systems, add --build=ppc64le
make
make install
cd /tmp && rm -rf nagios-plugins-2.2.1/ nagios-plugins-2.2.1.tar.gz
# Install addon plugin for check_mem
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/justintime/nagios-plugins/master/check_mem/check_mem.pl && mv check_mem.pl check_mem && chmod +x check_mem
# All plugins will be located here: /usr/local/nagios/libexec/
```
#### CentOS 7 - ARM64
``` bash
yum -y install https://dl.fedoraproject.org/pub/epel/7/aarch64/e/epel-release-7-9.noarch.rpm
yum -y install gcc glibc glibc-common openssl-devel perl libdbi fping qstat bind-utils nagios-common
# Setup nagios user account
chsh -s /bin/bash nagios
su - nagios
install -d -m 0700 ~/.ssh && touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLCl3cMafCiSK76iiT3kktL55QN/Cx2Ub0HT8wnJtw0bNn00KP+k+2u7DLF8OSI6v8MESgXIy6EDaSS7I+/sGV8xn0GLWWTSU8eUJ9DK5U0MWuvYM7kBl2z8s9VcW2rnZziSJcdBvgT60TWS3QEAZG78XM2E/h3iTR1UpbaCcECnsztbMyc9iwySLuN8u1p+D/KFlfvi8jm5Jn87WKH8PbEO9KU45PkukdQ14xk7h4xzDmWHc7cZweK8NvpSmLu5Ql0OB8bdmU6n876r0oNa2BvGucQZvkdd4UoaWkLDQ/4EPqQUfPvAJxbmXeQDo8SzE6J/a283aU92McNVwpcTVF nagios@Ubuntu-1604-xenial-64-minimal" >> ~/.ssh/authorized_keys
# Downloading and install the plugin tools
cd /tmp && wget -r --no-parent --reject="index.html*" --cut-dirs=100 http://cbs.centos.org/kojifiles/packages/nagios-plugins/2.0.1/1.el7/aarch64/
cd cbs.centos.org && rm *all* *disk_smb* *ifoperstatus* *ifstatus* *ntp-perl* *pgsql* *radius* *sensors* *snmp*
rpm -ivh *
cd /tmp && rm -rf cbs.centos.org/
mkdir -p /usr/local/nagios/ && ln -s /usr/lib64/nagios/plugins /usr/local/nagios/libexec
# Install addon plugin for check_mem
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/justintime/nagios-plugins/master/check_mem/check_mem.pl && mv check_mem.pl check_mem && chmod +x check_mem
# All plugins will be installed here: /usr/lib64/nagios/plugins with symlink to: /usr/local/nagios/libexec
```
#### SmartOS - x86
``` bash
mkdir /tmp/nagios_in && cd /tmp/nagios_in
wget http://www.nagios-plugins.org/download/nagios-plugins-2.0.3.tar.gz
gunzip nagios-plugins-2.0.3.tar.gz
tar -xvf nagios-plugins-2.0.3.tar
cd nagios-plugins-2.0.3
./configure
make
make install

useradd -d /home/nagios -m nagios -s /bin/bash
groupadd nagios
chown nagios:nagios /usr/local/nagios/
chown -R nagios:nagios /usr/local/nagios/libexec/
# Set random password
passwd nagios 

su - nagios

install -d -m 0700 ~/.ssh && touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLCl3cMafCiSK76iiT3kktL55QN/Cx2Ub0HT8wnJtw0bNn00KP+k+2u7DLF8OSI6v8MESgXIy6EDaSS7I+/sGV8xn0GLWWTSU8eUJ9DK5U0MWuvYM7kBl2z8s9VcW2rnZziSJcdBvgT60TWS3QEAZG78XM2E/h3iTR1UpbaCcECnsztbMyc9iwySLuN8u1p+D/KFlfvi8jm5Jn87WKH8PbEO9KU45PkukdQ14xk7h4xzDmWHc7cZweK8NvpSmLu5Ql0OB8bdmU6n876r0oNa2BvGucQZvkdd4UoaWkLDQ/4EPqQUfPvAJxbmXeQDo8SzE6J/a283aU92McNVwpcTVF nagios@Ubuntu-1604-xenial-64-minimal" >> ~/.ssh/authorized_keys
exit

cd /tmp && rm -rf nagios_in/

# Install addon plugin for check_mem
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/justintime/nagios-plugins/master/check_mem/check_mem.pl && mv check_mem.pl check_mem && chmod +x check_mem

# permissions workaround required for SmartOS
chown root:root /usr/local/nagios/libexec/pst3
 
```
#### FreeBSD 
``` bash
mkdir /tmp/nagios_in && cd /tmp/nagios_in
wget http://www.nagios-plugins.org/download/nagios-plugins-2.0.3.tar.gz
gunzip nagios-plugins-2.0.3.tar.gz
tar -xvf nagios-plugins-2.0.3.tar
cd nagios-plugins-2.0.3
./configure
make
make install

adduser
#nagios
#nagios
#enter
#enter
#enter
#enter
#bash
#enter
#enter
#enter
#enter
#yes
#enter
#yes
#no

su - nagios

install -d -m 0700 ~/.ssh && touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLCl3cMafCiSK76iiT3kktL55QN/Cx2Ub0HT8wnJtw0bNn00KP+k+2u7DLF8OSI6v8MESgXIy6EDaSS7I+/sGV8xn0GLWWTSU8eUJ9DK5U0MWuvYM7kBl2z8s9VcW2rnZziSJcdBvgT60TWS3QEAZG78XM2E/h3iTR1UpbaCcECnsztbMyc9iwySLuN8u1p+D/KFlfvi8jm5Jn87WKH8PbEO9KU45PkukdQ14xk7h4xzDmWHc7cZweK8NvpSmLu5Ql0OB8bdmU6n876r0oNa2BvGucQZvkdd4UoaWkLDQ/4EPqQUfPvAJxbmXeQDo8SzE6J/a283aU92McNVwpcTVF nagios@Ubuntu-1604-xenial-64-minimal" >> ~/.ssh/authorized_keys
exit

cd /tmp && rm -rf nagios_in/
```
#### Mac OSX (Mac OS client machines are using NRPE)
``` bash
# If you haven't already done so, accept the xcode license agreement
xcodebuild -license
# Install homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
# Download and install the Nagios Mac OS X Agent
cd /tmp
curl -o macosx-nrpe-agent.tar.gz https://assets.nagios.com/downloads/nagiosxi/agents/macosx-nrpe-agent.tar.gz
tar -zxvf macosx-nrpe-agent.tar.gz
cd macosx
sudo ./fullinstall
# When prompted for "Allow from:" enter:
nagios.adoptopenjdk.net
# Install addon plugin for check_mem
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/justintime/nagios-plugins/master/check_mem/check_mem.pl && mv check_mem.pl check_mem && chmod +x check_mem
```

#### AIX 5.3/6.1/7.2 Power PC
``` bash
mkdir /tmp/nagiostmp && cd /tmp/nagiostmp 
wget http://www.gbl-software.de/nagiosbinaries/AIX-5.3-plugins-1.4.14-binaries.tar.gz
gzip -d AIX-5.3-plugins-1.4.14-binaries.tar.gz
tar -xvf AIX-5.3-plugins-1.4.14-binaries.tar 
cd usr && mv local /usr/
cd /usr/local/nagios/libexec
rm -rf /tmp/nagiostmp
mkuser home="/home/nagios" shell="/usr/bin/ksh" nagios
su - nagios
mkdir ~/.ssh && touch ~/.ssh/authorized_keys 
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys 
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLCl3cMafCiSK76iiT3kktL55QN/Cx2Ub0HT8wnJtw0bNn00KP+k+2u7DLF8OSI6v8MESgXIy6EDaSS7I+/sGV8xn0GLWWTSU8eUJ9DK5U0MWuvYM7kBl2z8s9VcW2rnZziSJcdBvgT60TWS3QEAZG78XM2E/h3iTR1UpbaCcECnsztbMyc9iwySLuN8u1p+D/KFlfvi8jm5Jn87WKH8PbEO9KU45PkukdQ14xk7h4xzDmWHc7cZweK8NvpSmLu5Ql0OB8bdmU6n876r0oNa2BvGucQZvkdd4UoaWkLDQ/4EPqQUfPvAJxbmXeQDo8SzE6J/a283aU92McNVwpcTVF nagios@Ubuntu-1604-xenial-64-minimal" >> ~/.ssh/authorized_keys
exit

# Install addon plugin for check_mem
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/justintime/nagios-plugins/master/check_mem/check_mem.pl && mv check_mem.pl check_mem && chmod +x check_mem
```
* [[Additional steps for Firewalled or Nat'ed Nagios Clients]]
* [[Clients that have ICMP Disabled]]

## Nagios Server Side:
#### Test password-less key based authentication to the client machine
``` bash
su - nagios
# Change *HOSTNAME* to the hostname of the client
# accept ssh key
ssh nagios@*HOSTNAME*
```
#### Create a configuration file for the new client machine
``` bash
# Change *HOSTNAME* to the hostname of the client
cp ~/template.cfg  /usr/local/nagios/etc/servers/*HOSTNAME*.cfg
sed 's/ReplaceHostName/*HOSTNAME*/' -i /usr/local/nagios/etc/servers/*HOSTNAME*.cfg
# Manually update 'alias" and 'address' fields 
# Optional update icon fields
vi /usr/local/nagios/etc/servers/*HOSTNAME*.cfg
```

* [[Monitoring Additional Services]]

#### Restart nagios to reload config
``` bash
# Run Preflight checks to ensure everything is good
~/pre-flight.sh
# If all is good restart nagios
/etc/init.d/nagios restart
````
#### Log into the web site and take a look
http://nagios.adoptopenjdk.net