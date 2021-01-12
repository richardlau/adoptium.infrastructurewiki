# Manual Setup for Nagios Clients

If, for whatever reason, a server needs to be monitored, but can't be setup via the [Nagios_Ansible_Config_Tool](https://github.com/AdoptOpenJDK/openjdk-infrastructure/blob/master/ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_tool/Nagios_Ansible_Config_tool.sh), the following steps can be done manually.

## Client Configuration:

The process to configure the client machines is fairly simple. However, the implementation will vary slightly by OS and architecture. 

Key requirements:

   - Local user named ‘nagios’
   - SSH public key authentication (authorized_keys)
   - Nagios plugins

### Setup Nagios User

This will vary depending on the OS, but the following rules need to be satisfied:

   - The user must be called 'nagios'
   - The user must have permissions to access `/usr/local/nagios/libexec/` 
   - The user must have a `~/.ssh/authorised_keys` file defined, containing the Nagios Server's Nagios user's `id_rsa.pub`

The ssh key can be copied to the Nagios Server via: `ssh-copy-id -i ~/.ssh/id_rsa.pub nagios@nagios.adoptopenjdk.net`

### Install Nagios-Plugins:

Most distributions are able to install the `nagios-plugins` from their package manager.

i.e. On Ubuntu 20.04 / Linux Mint 20
```bash
sudo apt install nagios-plugins
```

If this isn't possible on your distribution, the common Nagios plugins can be built by following [this guide](https://support.nagios.com/kb/article.php?id=569). 

Once this has been done, ensure that the plugins can be accessed at `/usr/local/nagios/libexec` - if this is not the case, symlink the folder to that location:

`ln -s /usr/lib/nagios/plugins /usr/local/nagios/libexec`

## Server Definition

With the client setup, the Nagios server needs to be told to monitor the client, and what services to monitor. The template for what we typically monitor a client for can be found [here](https://github.com/AdoptOpenJDK/openjdk-infrastructure/blob/master/ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_tool/templates/template.cfg), with additional service definitions found in [here](https://github.com/AdoptOpenJDK/openjdk-infrastructure/tree/master/ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_tool/templates). An overview of the checks we use can be found in the [overview page](https://github.com/AdoptOpenJDK/openjdk-infrastructure/wiki/Overview-of-Nagios#what-are-the-checks-we-use).  Copy and paste the templates / service checks required into a file at `/usr/local/nagios/etc/servers/$HOST_NAME.cfg`, on the Nagios Server. 

Different distributions will require different checks, depending on their package manager, and the method in which they sync time. For distributions that use `systemd.timesyncd`, use the `check_timesync.cfg` template. For distributions using `NTP`, use the `check_ntp_timesync.cfg` template.

**TODO: Add a table of items that need to be changed in the templates and examples of what values they need to be**

**TODO: Add a part about the `/usr/local/nagios/etc/objects/hostgroups.cfg` file being updated**

After the server definition has been made, it can be checked by running `/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg`, and if no errors have occurred, the service can be restarted by running `sudo service nagios restart`

# Additional Steps for Firewalled or NAT'd clients

There are additional steps that are required to complete the client setup for systems where the Nagios server doesn't have direct access to them. (NAT'ed, Firewalled, etc) The following steps will configure a Reverse SSH Tunnel from the client system to the Nagios server allowing it to monitor the client across the tunnel. These steps assume the 'Nagios Client Agent configuration' has been completed.

#### Create a script to establish a reverse SSH tunnel
``` bash
vi ~/Nagios_RemoteTunnel.sh
```
**Change REMOTE_PORT**
``` bash
#!/bin/bash
# script to establish Reverse SSH Tunnel to the Nagios server
# Workaround for clients where the Nagios server don't have a directly connect (NAT, Firewalled, etc) 
#
# Vars
REMOTE_HOST="nagios.adoptopenjdk.net"
USER_NAME="nagios"
REMOTE_PORT="222*"
LOCAL_PORT="22"
LOGIN_PORT="22"
IDENTITY_KEY="~/.ssh/id_rsa"
# Command
Reverse_Tunnel="ssh -o StrictHostKeyChecking=no -f -n -N -R $REMOTE_PORT:127.0.0.1:$LOCAL_PORT $USER_NAME@$REMOTE_HOST -p $LOGIN_PORT -i $IDENTITY_KEY"
# Running? if not start it
pgrep -f -x "$Reverse_Tunnel" > /dev/null 2>&1 || $Reverse_Tunnel
```
####  Make the script executable
``` bash
chmod +x Nagios_RemoteTunnel.sh
```
####  Ensure its always running
```
crontab -e
* * * * * ~/Nagios_RemoteTunnel.sh
```

## Nagios Server Side:
#### Test the Reverse SSH Tunnel connection
``` bash
# Change *PORTNUMBER* to the remote port number set above
/usr/local/nagios/libexec/check_by_ssh -H 127.0.0.1 -p *PORTNUMBER* -n lh -s c1:c2:c3 -C uptime -C uptime -C uptime
```
#### Manually update the host config file
``` bash
vi /usr/local/nagios/etc/servers/*HOSTNAME*.cfg
# Change the 'address' field to:
127.0.0.1 -p *PORTNUMBER*
```

#### Restart nagios to reload config
``` bash
# Run Preflight checks to ensure everything is good
~/pre-flight.sh
# If all is good restart nagios
/etc/init.d/nagios restart
````
#### Log into the web site and take a look
http://nagios.adoptopenjdk.net
