# What is Nagios?

[Nagios](https://www.nagios.org/) is an industry standard server monitoring service, allowing an at-a-glance overview of machines in their server farm. It allows flexibility for users to specify exactly what aspects of each machine they want to monitor, along with how often they are checked. 

# What are the checks we use?

For most typical build and test machines, we have the following checks in place:

| Check | How Often | Warning Boundary | Critical Boundary |
|-|-|-|-|
| Check SSH | Every 15 minutes | - | Can't connect to machine |
| Current Load | Every 30 minutes | 15,10,5 | 30,24,20 |
| Disk Space Root Partition | Every 60 minutes | 20% free | 10% free |
| Check Jenkins connection | Every 30 minutes | Temporarily disabled | Fully disconnected|
| Ping | Every 15 minutes | rta 200, 20% packet loss | rta 500, 60% packet loss |
| Check RAM | Every 10 minutes | 15% free | 5% free |
| **Check Timesync** | Every 15 minutes | Time not synchronized / service not running | Can't find required info |
| **Check Package Manager** | Once a day  | Any updates required | Critical Updates required |

The `check_ssh` check output defines if the host is considered connected to the Nagios Server. If this is critical, it can be assumed no other checks will work either.

**Note:** For the checks in bold, the checks are platform specific.

An up-to-date version of the checks can be found in [_ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_Tool/templates_](https://github.com/AdoptOpenJDK/openjdk-infrastructure/tree/master/ansible/playbooks/Supporting_Scripts/Nagios_Ansible_Config_tool/templates)