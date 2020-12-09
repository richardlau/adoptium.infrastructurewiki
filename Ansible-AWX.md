# Ubuntu server setup:

- apt-get update
- apt install git docker.io python3-pip libffi-dev 
- useradd -g docker -m awxgit

As the new ansible user:

- git clone https://github.com/ansible/awx -b 15.0.1 (latest at time of writing)
- openssl req -x509  -nodes -days 90  -newkey rsa:4096  -keyout server.key -out server.crt  -subj "/C=GB/ST=UK/L=London/O=AdoptOpenJDK/CN=awx.adoptopenjdk.net"
- Edit `awx/installer/inventory` and update `ssl_certificate` and `ssl_certificate_key` to the full path to the two files created from the openssl command above. Also change `admin_password` to something other than `password` (but probably not something with `!` in it)
- pip3 install docker-compose ansible
- export PATH=$HOME/.local/bin:$PATH
- cd awx/installer
- ansible-playbook -i inventory install.yml
- docker logs -f awx_task (Don't skip this - it should have a message with `127.0.0.1 | SUCCESS` fairly quickly then after five minutes or so)
- If anything goes wrong - clear it out `docker stop awx_task awx_web awx_postgres awx_redis` then `docker rm awx_task awx_web awx_postgres awx_redis` Wipe out `~awx/.awx` (will likely need root) and re-run the playbook after fixing anything obvious :-)

Once ansible is running, connect to it on the standard SSL port (443)

Some of the playbooks we use reference the `/Vendor_Files` directory. This needs to be present in the `ansible/awx_task` docker container: Put the `Vendor_Files` directory on the host and run `docker cp Vendor_Files awx_task:/Vendor_Files`

# Setting up the configuration

I will run through each of the concepts that AWX provides and explain how each of these are configured:

## Authentication

Either
- Create users manually in the "Users" section
- Go to Settings -> Authentication -> Github and set up a GitHub team ([Instructions here](https://docs.ansible.com/ansible-tower/latest/html/administration/social_auth.html#github-oauth2-settings)) with team ID 2342525 and organization map: `{"AdoptOpenJDK":{"admins":true}}` and team map `{"AdoptOpenJDK":{"organization":"AdoptOpenJDK","users":true,"remove":false}}`

## Organization

Create a new organisation called `AdoptOpenJDK`

## Project

The project connects AWX with a github repository. Multiple ones can be created if you want to run things from your own fork/branch of the openjdk-infrastructure repository, but most of the time we want to run from the default one. Set up the project as follows:
- NAME: adoptopenjdk/openjdk-infrastructure
- ORGANIZATION: AdoptOpenJDK
- SCM TYPE: Git
- SCM URL: https://github.com/adoptopenjdk/openjdk-infrastructure
- SCM UPDATE OPTIONS: `CLEAN` and `UPDATE REVISION ON LAUNCH`

## Inventory

Inventories define the set of machines that we run against. For the openjdk-infrastructure repository the master list of machines is stored in the `ansible/inventory.yml` file. This is not in the format which AWX expects, so we another script to parse it. Create an inventory as follows:

- NAME: Dynamic inventory from github
- ORGANISATION: AdoptOpenJDK

Under the `SOURCES` tab you will need to create a new entry, which I have called `Dynamic inventory source` which specifies how to populate the inventory. Use the following settings:

- NAME: Dynamic inventory source
- SOURCE: Sourced from a Project
- PROJECT: adoptopenjdk/openjdk-infrastructure
- INVENTORY FILE: `ansible/plugins/adoptopenjdk_yaml.py`
- UPDATE OPTIONS: `OVERWRITE` and `OVERWRITE_VARIABLES`

You can also use the `SCHEDULES` tab within the source definition to tell AWX to resync periodically with github, or you can re-run when you update the entries.

Once the inventory source is saved and refreshed (click the `recycle` icon in the actions for the source) you should see the `GROUPS` an `HOSTS` tabs of the inventory populated with all of the machines. `GROUPS` will have sections for `build`, `docker`, `infrastructure` and `test` (at the time of writing) and `HOSTS` will show each system as well as the group they are in. Each host can be enabled or disabled using the switches to the left of the hostname.

## CREDENTIALS

Credentials allow AWX to connect to other systems. We will need at least one ssh credential to allow it to connect to the machines which it is going to deploy onto (this will be put on the machines via Bastillion). Create the credential with the following parameters:

- NAME: `ssh admin key`
- DESCRIPTION: log into machines over ssh
- CREDENTIAL TYPE: Machine
- USERNAME: root
- SSH PRIVATE KEY: <Obviously this is private!>
- PRIVATE KEY PASSPHRASE: <This is private too!>

## TEMPLATES

Templates are the entities that do the work of deploying playbooks. We create one as follows:

- NAME: Deploy UNIX playbook
- JOB TYPE: Run
- PROJECT: adoptopenjdk/openjdk-infrastructure
- PLAYBOOK: ansible/playbooks/AdoptOpenJDK_Unix_Playbook/main.yml
- CREDENTIALS: `ssh admin key`
- SKIP TAGS: `adoptopenjdk` and `hosts_file` (for now)
- LABELS: `build`
- EXTRA_VARIABLES: `ansible_ssh_user: root`

The template can also have a schedule associated with it if you want to run it periodically, or you can run it manually by clicking the rocket ship icon next to it in the list.

# Backups

Two options:
- Back up the docker containers
- Using `./tower-cli receive --all -h localhost -u admin -p ASK --insecure` (`pip3 install ansible_tower_cli` if you don't have it)
