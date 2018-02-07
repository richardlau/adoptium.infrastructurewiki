# Why would I want to?

The intention of AdoptOpenJDK is to provide binaries and hardware access for all of the major platforms, for the purposes of...

- Allowing end users to have a reliable source of OpenJDK binaries for all platforms.
- Research and Development by OpenJDK developers, academics and researchers.
- An open, common, audited, build infrastructure for vendors to use (if they wish).
- Providing a place to try out build infrastructure ideas that might one day be re-implemented in the OpenJDK project.

To achieve this, we need people to help us maintain the infrastructure, tools and systems we use and rely on. If this is something you'd like to get involed with, this document provides an overview of how to get started.

If you want to get involved, the [Get Involved](https://adoptopenjdk.net/getinvolved.html) article in the AdoptOpenJDK blog is a good place to start. Also, you should read the the [AdoptOpenJDK Infrastructure Onboarding Guide](https://github.com/AdoptOpenJDK/openjdk-infrastructure/blob/master/ONBOARDING.md) in the AdoptOpenJDK Infrastructure repository at github.com.

The rest of this document describes what else you need to know and do.

# The AdoptOpenJDK Infrastructure GitHub repository

- The repository is at https://github.com/AdoptOpenJDK/openjdk-infrastructure.
- If you're reading this document, you already have access to it.
- You need to be added to the [infrastructure](https://github.com/orgs/AdoptOpenJDK/teams/infrastructure) team. To request to be added, go to the [AdoptOpenJDK Infrastructure](https://github.com/AdoptOpenJDK/openjdk-infrastructure) repository and create an issue requesting to be added to the team. The issue I created is [here](https://github.com/AdoptOpenJDK/openjdk-infrastructure/issues/156) if you want an example. Anyone in the [admin_infrastructure](https://github.com/orgs/AdoptOpenJDK/teams/admin_infrastructure/members) team will be able to approve your request and add you to the team. The approval itself does not automatically add you - that's there for traceability. As part of the approval, members of the [admin_infrastructure](https://github.com/orgs/AdoptOpenJDK/teams/admin_infrastructure/members) team will also add you to the team.

# KeyBox

- Provides ssh key management.
- See [SSH Key Management with KeyBox](https://blog.adoptopenjdk.net/2017/08/ssh-key-management-keybox) in the AdoptOpenJDK blog for more details about how we use KeyBox.
- Used to control access to AdoptOpenJDK machines.
- Add your personal public ssh key to allow you to log on to the AdoptOpenJDK machines using your ssh key.
- Ask a member of the [admin_infrastructure](https://github.com/orgs/AdoptOpenJDK/teams/admin_infrastructure/members) team to add your public ssh key.

# Nagios

- Monitors the AdoptOpenJDK machines.
- Web interface at https://nagios.adoptopenjdk.net/.
  - Ask a member of the [admin_infrastructure](https://github.com/orgs/AdoptOpenJDK/teams/admin_infrastructure/members) team for access.
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
- We use a tool called `dotgpg` to create, encryppt, decrypt, edit and view these files.
  - Instructions for how to set this up this tool [here](https://github.com/AdoptOpenJDK/secrets/blob/master/README.md).

# Slack

Join the following team / channels...

- [adoptopenjdk.slack.com](https://adoptopenjdk.slack.com/)
  - [#infrastructure](https://adoptopenjdk.slack.com/messages/C53GHCXL4)
  - [#infrastructure-bot](https://adoptopenjdk.slack.com/messages/C8C212BU6)


