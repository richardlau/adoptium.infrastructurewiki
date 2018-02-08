# Why would I want to?

The intention of AdoptOpenJDK is to provide binaries and hardware access for all of the major platforms, for the purposes of...

- Allowing end users to have a reliable source of OpenJDK binaries for all platforms.
- Research and Development by OpenJDK developers, academics and researchers.
- An open, common, audited, build infrastructure for vendors to use (if they wish).
- Providing a place to try out build infrastructure ideas that might one day be re-implemented in the OpenJDK project.

To achieve this, we need people to help us maintain the infrastructure, tools and systems we use and rely on. If this is something you'd like to get involved with, this document provides an overview of how to get started.

If you want to get involved, please read on...

# Join the AdoptOpenJDK GitHub Organisation

This is at https://github.com/AdoptOpenJDK. It's an open community so you should be able to access it. If you can't, please contact a member of the `admin-infrastructure` team (see the [contacts](https://github.ibm.com/runtimes/infrastructure/wiki/Contributing-to-AdoptOpenJDK-Infrastructure#contacts) section below).

# Join the infrastructure team

The `infrastructure` team repository is at https://github.com/AdoptOpenJDK/openjdk-infrastructure. To contribute to it, you'll need to join the `infrastructure` team. To do this, go to the [AdoptOpenJDK Infrastructure repository](https://github.com/AdoptOpenJDK/openjdk-infrastructure) and create an issue requesting to be added. The issue I created is [here](https://github.com/AdoptOpenJDK/openjdk-infrastructure/issues/156) if you want an example.

A member of the `admin-infrastructure` section team will approve the issue and add you to the team. The approval itself does not automatically add you - that's there for traceability. You'll have to wait to be added.

# More onboarding instructions

Please read the following guides...
- [Get Involved](https://adoptopenjdk.net/getinvolved.html) (in the AdoptOpenJDK blog).
- [AdoptOpenJDK Infrastructure Onboarding Guide](https://github.com/AdoptOpenJDK/openjdk-infrastructure/blob/master/ONBOARDING.md) (in the AdoptOpenJDK GitHub wiki).

If you are not able to access those documents, please contact any member of the `admin-infrastructure` teams.

The rest of this document describes what else you need to do.

# KeyBox

- Provides ssh key management.
- See [SSH Key Management with KeyBox](https://blog.adoptopenjdk.net/2017/08/ssh-key-management-keybox) in the AdoptOpenJDK blog for more details about how we use KeyBox.
- Used to control access to AdoptOpenJDK machines.
- Add your personal public ssh key to allow you to log on to the AdoptOpenJDK machines using your ssh key.
- Ask a member of the `admin_infrastructure` team to add your public ssh key.

# Nagios

- Monitors the AdoptOpenJDK machines.
- Web interface at https://nagios.adoptopenjdk.net/.
  - Ask a member of the `admin_infrastructure` team for access.
  - Log on using the credentials created for you.
- nagios.adoptopenjdk.net.
  - Log on using the userid and SSH key you added to KeyBox.

# Ansible AWX

- Where the various build jobs run.
- Web interface at http://ansible.adoptopenjdk.net/.
- Log on using your GitHub credentials.

# Jenkins

- Web interface at https://ci.adoptopenjdk.net/.
- Log on using your GitHub credentials.

# Secrets and dotgpg

- We have a secrets repository at https://github.com/AdoptOpenJDK/secrets.
- We use it to hold files that have been encrypted.
- We use a tool called `dotgpg` to create, encrypt, decrypt, edit and view these files.
  - Instructions for how to set this up this tool [here](https://github.com/AdoptOpenJDK/secrets/blob/master/README.md).

# Slack

Follow us on...

<table>
  <tr>
    <th> Team </th>
    <th colspan="2"> Channels </th>
  </tr>
  <tr>
    <td> <a href="https://adoptopenjdk.slack.com/"> adoptopenjdk.slack.com </a> </td>
    <td> <a href="https://adoptopenjdk.slack.com/messages/C53GHCXL4"> #infrastructure </a> </td>
    <td> <a href="https://adoptopenjdk.slack.com/messages/C8C212BU6"> #infrastructure-bot </a> </td>
  </tr>
</table>

# Contacts

## The `admin-infrastructure` team ( [GitHub](https://github.com/orgs/AdoptOpenJDK/teams/admin_infrastructure/members) )

<table>
  <tr>
    <th> Member </th>
    <th> Slack </th>
    <th> GitHub </th>
  </tr>
  <tr>
    <td> Brad Blondin </td>
    <td> @bblondin </td>
    <td> <a href="https://github.com/bblondin"> https://github.com/bblondin </a> </td>
  </tr>
  <tr>
    <td> George Adams </td>
    <td> @gdams </td>
    <td> <a href="https://github.com/gdams"> https://github.com/gdams </a> </td>
  </tr>
</table>

## The `infrastructure` team( [GitHub](https://github.com/orgs/AdoptOpenJDK/teams/infrastructure/members) )

<table>
  <tr>
    <th> Member </th>
    <th> Slack </th>
    <th> GitHub </th>
  </tr>
  <tr>
    <td> Brad Blondin </td>
    <td> @bblondin </td>
    <td> <a href="https://github.com/bblondin"> https://github.com/bblondin </a> </td>
  </tr>
  <tr>
    <td> George Adams </td>
    <td> @gdams </td>
    <td> <a href="https://github.com/gdams"> https://github.com/gdams </a> </td>
  </tr>
  <tr>
    <td> Geraint Jones </td>
    <td> @Geraint </td>
    <td> <a href="https://github.com/geraintwjones"> https://github.com/geraintwjones </a> </td>
  </tr>
  <tr>
    <td> Peter Stanton </td>
    <td> @Peter Stanton </td>
    <td> <a href="https://github.com/pnstanton"> https://github.com/pnstanton </a> </td>
  </tr>
  <tr>
    <td> Stewart X Addison </td>
    <td> @sxa </td>
    <td> <a href="https://github.com/sxa555"> https://github.com/sxa555 </a> </td>
  </tr>
  <tr>
    <td> Tim Ellison </td>
    <td> @tpellison </td>
    <td> <a href="https://github.com/tellison"> https://github.com/tellison </a> </td>
  </tr>
</table>

Membership of these teams will change over time. Please refer to the team link for the latest list of members.
