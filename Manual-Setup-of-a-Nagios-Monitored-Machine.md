# Manual Setup for Nagios Clients

If, for whatever reason, a server needs to be monitored, but can't be setup via the [Nagios_Ansible_Config_Tool](https://github.com/AdoptOpenJDK/openjdk-infrastructure/blob/master/ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_tool/Nagios_Ansible_Config_tool.sh), the following steps can be done manually.

## Client Configuration:

The process to configure the client machines is fairly simple. However, the implementation will vary slightly by OS and architecture. 

Key requirements:

   - Local user named ‘nagios’
   - SSH public key authentication (authorized_keys)
   - Nagios plugins

### Setup Nagios User:

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

## Server Definition:

With the client setup, the Nagios server needs to be told to monitor the client, and what services to monitor. The template for what we typically monitor a client for can be found [here](https://github.com/AdoptOpenJDK/openjdk-infrastructure/blob/master/ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_tool/templates/template.cfg), with additional service definitions found in [here](https://github.com/AdoptOpenJDK/openjdk-infrastructure/tree/master/ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_tool/templates). An overview of the checks we use can be found in the [overview page](https://github.com/AdoptOpenJDK/openjdk-infrastructure/wiki/Overview-of-Nagios#what-are-the-checks-we-use).  Copy and paste the templates / service checks required into a file at `/usr/local/nagios/etc/servers/*HOSTNAME*.cfg`, on the Nagios Server. 

Different distributions will require different checks, depending on their package manager, and the method in which they sync time. For distributions that use `systemd.timesyncd`, use the `check_timesync.cfg` template. For distributions using `NTP`, use the `check_ntp_timesync.cfg` template.

There are several items that will need to be changed in these template files, to make the server definition specific to the machine. Here's a table of the values that need to be changed:

| Template String         | Changed to                                             | Example                           |
|-------------------------|--------------------------------------------------------|-----------------------------------|
| ReplaceHostName         | The machines name as it appears in the `inventory.yml` | `build-spearhead-freebsd12-x64-1` |
| ReplaceAliasDescription | A description of the machine                           | `Add by Ansible`                  |
| ReplaceIPAddress        | IP Address of the Machine                              | `185.131.222.224`                 |

Note: The `Add by Ansible` message is set when the machine has automatically been added via Ansible. When manually setting up machines, the description may be more important. i.e: `ci.adoptopenjdk.net`'s message is `AdoptOpenJDK - Jenkins server`.

When adding a new machine to be monitored, it's important to add it to the correct hostgroup in the `/usr/local/nagios/etc/objects/hostgroups.cfg` file. If the machine is part of an already defined hostgroup (i.e. `spearhead` , `ibmcloud`, `marist`, etc), then all that's required is to add the hostname to the group's `members` field. If the machine is a new hostgroup / from a new provider, the hostgroup needs to be created, with the following template

```bash
define hostgroup{
        hostgroup_name  <Provider_Name>
        alias           <Provider_Name>
        members         ,<Newly_Added_Machine_Hostname>
        }
```

After the server definition has been made and host groups updated, syntax and the rest of the nagios config files can be checked by running `/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg`. If no errors have occurred, the service can be restarted by running `sudo service nagios restart` and the machine should be viewable at [nagios.adoptopenjdk.net](https://nagios.adoptopenjdk.net/nagios/)

### Clients with ICMP Disabled:

If a client has ICMP disabled and they are 'unpingable', the `ping` service will have to be removed from the server definition.

## Additional Steps for Firewalled or NAT'd clients:

There are additional steps that are required to complete the client setup for systems where the Nagios server doesn't have direct access to them. (NAT'ed, Firewalled, etc) The following steps will configure a Reverse SSH Tunnel from the client system to the Nagios server allowing it to monitor the client across the tunnel. These steps assume the `Client Configuration` section has been completed.

**All of these commands should be done as the Nagios user, on the respective machines**

- Copy the [Nagios_RemoteTunnel.sh](https://github.com/AdoptOpenJDK/openjdk-infrastructure/blob/master/ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_tool/Nagios_RemoteTunnel.sh) script to the client machine at `~/Nagios_RemoteTunnel.sh`.

- Ensure the script is always running
```
crontab -e
* * * * * ~/Nagios_RemoteTunnel.sh
```

### Nagios Server Side:

- Test the Reverse SSH Tunnel connection

``` bash
# Change *PORTNUMBER* to the remote port number set in the script
/usr/local/nagios/libexec/check_by_ssh -H 127.0.0.1 -p *PORTNUMBER* -n lh -s c1:c2:c3 -C uptime -C uptime -C uptime
```

- Manually update the server definition file at `/usr/local/nagios/etc/servers/*HOSTNAME*.cfg` , so the `address` field instead has `127.0.0.1 -p *PORTNUMBER*`

- Check the Nagios config and restart if no errors occur

``` bash
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
# If all is good restart nagios
service nagios restart
````

If all went well, the machine should be viewable at [nagios.adoptopenjdk.net](https://nagios.adoptopenjdk.net/nagios/)
