## Project description

This project involves improvements to how we monitor the machines at the open source project.  It involves assessment and configuration of the Nagios server at the project.  

Nagios is a configurable server monitoring tool, monitoring elements such as connectivity, memory & cpu usage, disk space usage, system daemons etc. It consists of a monitoring server with agents on each remote host to be monitored. The aim of this project is to automate the Nagios server side configuration, by utilising  a script/scripts to handle the creation, and manipulation of the server side configuration files. In addition the creation of a tool to validate and report errors in those files.

## Project Timeline

### Before 5th December;
- Attending community calls, reading project documentation, updating local instances, contributing to the community issues and making pull requests

### Month 1

- Familiarization with the Adoptium community, and dive in on project MonCon
  - Dive in on the infrastructure repository and understand ,learn the processes set in place.
  - Review the project “MonCon” to better breakdown the milestones with mentor
  - setup nagios server locally and work on adding necessary scripts

- Investigate Adding Of Windows Hosts To Nagios
  - Learn and document how to install and configure monitoring for windows machines,

### Month 2

- Improve the nagios playbooks for the server configuration as required,
- Update the main infrastructure ansible playbooks for windows to do any necessary installation and configuration,and then finally testing and
deploying all of this.
- Automate the creation of the nagios configuration for these windows machines, ie creating hostgroups, services etc.

### Month 3
- Automate Installation & Configuration Of Nagios Nodes
  - Update server related global config files
  - Automate the Nagios server side configuration, by utilizing a script/scripts to handle the creation, and manipulation of the server side
configuration files. In addition the creation of a tool to validate and report errors in those files.
  - Identify additional monitoring tasks, and automate adding them via ansible.

- Update documentation, and necessary automation improvements we find as we worked through the first two months of the term.

### Final submission of Project MonCon 
- End of term: March 3, 2023 



## Participants

Intern: Lumu

Mentors: Scott Fryer, Haroon Khel, Shelley Lambert
