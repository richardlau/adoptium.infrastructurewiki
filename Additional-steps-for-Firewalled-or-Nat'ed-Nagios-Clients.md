###### There are additional steps that are required to complete the client setup for systems where the Nagios server doesn't have direct access to them. (NAT'ed, Firewalled, etc) The following steps will configure a Reverse SSH Tunnel from the client system to the Nagios server allowing it to monitor the client across the tunnel. These steps assume the 'Nagios Client Agent configuration' has been completed.

## Client Configuration: 
#### Configure the nagios user account
``` bash
# Become the nagios user and create a key
su - nagios
ssh-keygen -t rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub nagios@nagios.adoptopenjdk.net
```
#### Ensure ssh connects to the Nagios server works without a password
``` bash
ssh nagios@nagios.adoptopenjdk.net
exit
```
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