###### For clients that have ICMP Disabled (ie. ping is blocked) additional configuration is required to change the way Nagios detects the client system is alive.

#### Manually update the host config file
``` bash
vi /usr/local/nagios/etc/servers/*HOSTNAME*.cfg
# In the 'define host' section add the following line
check_command                   noping
# In the 'define service' section for 'PING' change the 'check_command' to:
check_command			noping
```

### Restart nagios to reload config
``` bash
# Run Preflight checks to ensure everything is good
~/pre-flight.sh
# If all is good restart nagios
/etc/init.d/nagios restart
```

#### Log into the web site and take a look
http://nagios.adoptopenjdk.net