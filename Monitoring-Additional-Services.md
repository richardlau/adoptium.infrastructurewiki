#### Monitoring Additional Services: Client specific services

##### Web Services:
check_http vs https
``` bash
define service{
        use                             generic-service      
        host_name                       HOSTNAME
        service_description             HTTP
        check_command                   check_http
        }
```
``` bash
define service {
        use                             generic-service
        host_name                       HOSTNAME
        service_description             HTTPS
        check_command                   check_https_url
        }
```

##### apt-get
###### apt is a built in plugin that comes preinstalled 
###### Define the service on the Nagios server
``` bash
define service{
        use                             generic-service
        host_name                       HOSTNAME
        service_description             Updates Required - apt
        check_command                   check_by_ssh!/usr/lib/nagios/plugins/check_apt
        notifications_enabled   0
        }
```

##### YUM
###### Ensure the yum Security plugin is installed - 'yum install yum-security' (RHEL5) or 'yum install yum-plugin-security' (RHEL6)
###### Edit the sudoers file and allow Nagios to use yum while restricting it to check-update only.
``` bash
nagios ALL = NOPASSWD: /usr/bin/yum --security check-update
```
###### Download the check_yum plugin and make it executable
``` bash
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/AdoptOpenJDK/openjdk-tests/master/Build/ansible/playbooks/nagios/additional_plugins/check_yum && chmod +x check_yum
```
###### Define the service on the Nagios server
``` bash
define service{
        use                             generic-service
        host_name                       HOSTNAME
        check_period                    once-a-day-at-8
        service_description             Updates Required - YUM
        check_command                   check_by_ssh!/usr/local/nagios/libexec/check_yum
        notifications_enabled   0
        }
```
##### DNF
###### Requires that ruby is installed on the machine
###### Download [check_dnf.rb](https://github.com/nikkolasg/check_dnf/blob/master/check_dnf.rb) to the machine in its plugin folder /usr/local/nagios/libexec.
###### Rename it to check_dnf and make it executable by nagios (chmod +x check_dnf)
###### Specify the *USERNAME* to login as and which ssh key to use
###### Ensure the key has been added to the Nagios server and the nagio user has access to it
``` bash
define service{
        use                             generic-service
        host_name                       HOSTNAME
        check_period			once-a-day-at-8
        service_description             Updates Required - DNF
        check_command                   check_ssh_dnf!*USERNAME*!/usr/local/nagios/etc/ssh_keys/*NAMEofKEY.key
        notifications_enabled   0
        }
```
##### Zypper
###### Edit the sudoers file and allow nagios to use zypper while restricting it to list-patches only.
``` bash
nagios ALL = NOPASSWD: /usr/bin/zypper list-patches
```
###### Download the check_yum plugin and make it executable
``` bash
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/AdoptOpenJDK/openjdk-tests/master/Build/ansible/playbooks/nagios/additional_plugins/check_zypper && chmod +x check_zypper
```

###### Define the service on the Nagios server
``` bash
define service{
        use                             generic-service
        host_name                       HOSTNAME
        check_period                    once-a-day-at-8
        service_description             Updates Required - Zypper
        check_command                   check_by_ssh!/usr/local/nagios/libexec/check_zypper
        notifications_enabled   0
        }
```

##### PKG (FreeBSD)
``` bash
```
###### Download the check_pkg plugin and make it executable
``` bash
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/AdoptOpenJDK/openjdk-tests/master/Build/ansible/playbooks/nagios/additional_plugins/check_pkg && chmod +x check_pkg
```

###### Define the service on the Nagios server
``` bash
define service{
        use                             generic-service
        host_name                       HOSTNAME
        check_period                    once-a-day-at-8
        service_description             Updates Required - Zypper
        check_command                   check_by_ssh!/usr/local/nagios/libexec/check_pkg 2>1 /dev/null
        notifications_enabled   0
        }
```
##### softwareupdate - MAC
###### Download the check_sw_up plugin and make it executable
``` bash
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/AdoptOpenJDK/openjdk-tests/master/Build/ansible/playbooks/nagios/additional_plugins/check_sw_up && chmod +x check_sw_up
```
###### Define the service on the Nagios server
``` bash
define service{
        use                             local-service
        host_name                       build-macstadium-macos1010-2
	check_period			once-a-day-at-8
        service_description             Updates Required - softwareupdate
        check_command                   remote_check_sw_up_mac
        notifications_enabled   0
        }

```
##### Account Passwd Expiry
###### Edit the sudoers file using 'visudo' and allow nagios to execute the chage command
``` bash
nagios ALL = NOPASSWD: /usr/bin/chage -l * 
```
###### Download and configure plugin
``` bash
# Ubuntu: 
apt-get install libmonitoring-plugin-perl libnagios-object-perl libnagios-plugin-perl
```
``` bash
# CentOS/OpenSuse:
yum -y install perl-Nagios-Plugin
zypper install perl-Nagios-Plugin
dnf -y install perl-Nagios-Plugin
# Edit the sudoers file and comment out "Defaults    requiretty"
```
``` bash
# OpenSuse: 
zypper -y install perl-Nagios-Plugin
```
``` bash
cd /usr/local/nagios/libexec && wget https://raw.githubusercontent.com/elio78/nagios/master/check_passwd_expiration.pl && mv check_passwd_expiration.pl check_passwd && chmod +x check_passwd
vi /usr/local/nagios/libexec/check_passwd
# replace line 1 with 
#!/usr/bin/perl -w
no warnings;
no warnings 'all';

# Test
su - nagios
/usr/local/nagios/libexec/check_passwd
```

``` bash
define service{
        use                             generic-service
        host_name                       HOSTNAME
        check_period			once-a-day-at-8
        service_description             Passwd Expiry
        check_command                   check_by_ssh!/usr/local/nagios/libexec/check_passwd -w 14 -c 3
        }
```