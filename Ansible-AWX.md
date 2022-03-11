# Ubuntu server setup:

- `apt-get update`
- `apt install git docker.io python3-pip libffi-dev`
- `useradd -G docker -m awx`

As the new `awx` user:

- `git clone https://github.com/ansible/awx -b 15.0.1` (latest branch at time of writing)
- (If you have your own SSL certificate for the web server you can bypass this self-signed certificate generation step - since the adopt one is fronted by CloudFlare it isn't a problem for us to use this one): `openssl req -x509  -nodes -days 1000 -newkey rsa:4096  -keyout server.key -out server.crt  -subj "/C=GB/ST=UK/L=London/O=AdoptOpenJDK/CN=awx.adoptopenjdk.net"`
- Edit `awx/installer/inventory` and update `ssl_certificate` and `ssl_certificate_key` to the full path to the two files created from the openssl command above (or ones from your own certificate). Also change `admin_password` to something other than `password` and also change `pg_password`
- `export PATH=$HOME/.local/bin:$PATH`
- `pip3 install docker-compose ansible` (This will take a few minutes)
- `cd awx/installer`

If you are going to be running via a cloudflare proxy, modify `awx/installer/roles/local_docker/templates/nginx.conf.j2` to comment out the `Content-Security-Policy` header, otherwise you will fall foul of HSTS violations.

- `ansible-playbook -i inventory install.yml`

You may receive a deprecation error concerning the `docker_service` module used in `roles/local_docker/tasks/upgrade_postgres.yml` causing the playbook to come to a halt. In this file, simply replace `docker_service:` with `docker_compose:` and re run the playbook.

- **Needed due to AWX bug:** Wait 2½ minutes then run the playbook again
- `docker logs -f awx_task` (Don't skip this - it should have a message with `127.0.0.1 | SUCCESS` fairly quickly then after five minutes or so)
- If anything goes wrong - clear it out `docker stop awx_task awx_web awx_postgres awx_redis` then `docker rm awx_task awx_web awx_postgres awx_redis` Wipe out `~awx/.awx` (will likely need root) and re-run the playbook after fixing anything obvious :-)
- If we still need to connect to any macos 10.10 boxes you [will need to enable](https://github.com/AdoptOpenJDK/openjdk-infrastructure/issues/1910#issuecomment-797628624) the `aes128-ctr` Cipher in the `/etc/ssh/ssh_config` of the `awx_task` container

Once ansible is running, connect to it on the standard SSL port (443)

TBC since some of this has been moved: Some of the playbooks we use reference the `/Vendor_Files` directory. This needs to be present in the `ansible/awx_task` docker container: Put the `Vendor_Files` directory on the host and run `docker cp Vendor_Files awx_task:/Vendor_Files`

# Setting up the configuration

I will run through each of the concepts that AWX provides and explain how each of these are configured:

## Organization

Create a new organisation called `AdoptOpenJDK`

## Authentication

Either
- Create users manually in the "Users" section
- Go to Settings -> Authentication -> Github and set up a GitHub team ([Instructions here](https://docs.ansible.com/ansible-tower/latest/html/administration/social_auth.html#github-oauth2-settings)) with team ID 2342525 and organization map: `{"AdoptOpenJDK":{"admins":true}}` and team map `{"AdoptOpenJDK":{"organization":"AdoptOpenJDK","users":true,"remove":false}}`
- The team ID in the previous paragraph can be retrieved via [the API]( but it is simpler to go to the web page for the team, look at the URL of the team icon which will have something like `/t/2342525?s=280&v=4` in it where the ID is visible. That one is from [infrastructure-secret](https://github.com/orgs/AdoptOpenJDK/teams/infrastructure-secret/members)

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
- ORGANISATION: Adoptium (Was AdoptOpenJDK)

The save, and under the `SOURCES` tab you will need to create a new entry, which I have called `Dynamic inventory source` which specifies how to populate the inventory. Use the following settings:

- NAME: Dynamic inventory source
- SOURCE: Sourced from a Project
- PROJECT: adoptium/infrastructure (Was adoptopenjdk/openjdk-infrastructure)
- INVENTORY FILE: `ansible/plugins/inventory/adoptopenjdk_yaml.py`
- UPDATE OPTIONS: `OVERWRITE` and `OVERWRITE_VARIABLES`

You can also use the `SCHEDULES` tab within the source definition to tell AWX to resync periodically with github, or you can re-run when you update the entries.

Once the inventory source is saved and refreshed (click the `recycle` icon in the actions for the source) you should see the `GROUPS` an `HOSTS` tabs of the inventory populated with all of the machines. `GROUPS` will have sections for `build`, `docker`, `infrastructure` and `test` (at the time of writing) and `HOSTS` will show each system as well as the group they are in. Each host can be enabled or disabled using the switches to the left of the hostname.

## CREDENTIALS

Credentials allow AWX to connect to other systems. We will need at least one ssh credential to allow it to connect to the UNIX-based machines which it is going to deploy onto (this will be put on the machines via Bastillion). Create the credential with the following parameters:

- NAME: `ssh admin key`
- DESCRIPTION: log into machines over ssh
- ORGANISATION: AdoptOpenJDK
- CREDENTIAL TYPE: Machine
- USERNAME: root
- SSH PRIVATE KEY: <Obviously this is private!>
- PRIVATE KEY PASSPHRASE: <This is private too!>

For Windows machiens we need to authenticate with a password. Since our passwords are different across each machine we create a credential which will prompt on each template deployment as follows:

- NAME: `Windows credentia`
- DESCRIPTION: Select pasword on launch
- ORGANISATION: AdoptOpenJDK
- CREDENTIAL TYPE: Machine
- USERNAME: Administrator
- PASSWORD: Blank with `Prompt on launch` checked.

## TEMPLATES

Templates are the entities that do the work of deploying playbooks. We create one as follows:

- NAME: Deploy UNIX playbook
- JOB TYPE: Run
- INVENTORY: Dynamic Inventory from Github
- PROJECT: adoptopenjdk/openjdk-infrastructure
- PLAYBOOK: ansible/playbooks/AdoptOpenJDK_Unix_Playbook/main.yml
- CREDENTIALS: `ssh admin key`
- SKIP TAGS: `nagios_master_config` `nagios_tunnel` (for now)
- EXTRA_VARIABLES: `ansible_ssh_user: root`

The template can also have a schedule associated with it if you want to run it periodically, or you can run it manually by clicking the rocket ship icon next to it in the list.

An equivalent template `Deploy AIX template` has the same values other then `PLAYBOOK` is set to `ansible/playbooks/AdopyOpenJDK_AIX_Playbook/main.yml`

For Windows deployments, we have the following template defined:

- NAME: Deploy Windows playbook
- JOB TYPE: Run
- INVENTORY: Dynamic Inventory from Github
- PROJECT: AdoptOpenJDK/openjdk-infrastructure
- PLAYBOOK: ansible/playbooks/AdoptOpenJDK_Windows_Playbook/main.yml
- CREDENTIALS: `Windows credential`
- SKIP TAGS: `basic_config` (for now)

# Vendor_Files

Some of our roles expect to be able to access the `/Vendor_Files` directory for some private information (alternatively it can be sourced from the `secrets` repo for those who have access. For the purposes of AWX, the `/Vendor_Files` directory should be located within the `awx_task` container - use `docker cp` if you need to update something within the container

# Backups

Three options:
- Back up the docker containers
- Using `./tower-cli receive --all -h localhost -u admin -p ASK --insecure` (`pip3 install ansible_tower_cli` if you don't have it)
- Or `./awx --conf.insecure export` (after pip3 install awxkit`)

# Update Ansible on the AWX server

Enter the `awx_task` docker container

`docker exec -it awx_task /bin/bash`

The version of Ansible used by the AWX server is the version of Ansible installed in this container. Updating Ansible is as easy on the AWX server as it is anywhere else.

`pip3 install --upgrade ansible`

You can specify a version of Ansible to upgrade to by using

`pip3 install --upgrade "ansible=x.x.x"`

If this doesn't work, you can try uninstalling and reinstalling Ansible in the docker container

`pip3 uninstall ansible`

`pip3 install ansible`

This will reinstall Ansible to the latest version

**Note:**

The update to a later Ansible version may cause an unexpected failure when you try to sync your inventory (this may not affect later versions of AWX)

While syncing your inventory, you may hit this error

```
File "/var/lib/awx/venv/awx/lib/python3.6/site-packages/awx/main/management/commands/inventory_import.py", line 142, in get_base_args
    if this_version >= Version('2.5'):
  File "/usr/lib64/python3.6/distutils/version.py", line 70, in __ge__
    c = self._cmp(other)
  File "/usr/lib64/python3.6/distutils/version.py", line 337, in _cmp
    if self.version < other.version:
TypeError: '<' not supported between instances of 'str' and 'int'
```

If so, go back into the `awx_task` container. Open up `/var/lib/awx/venv/awx/lib/python3.6/site-packages/awx/main/management/commands/inventory_import.py`
Look for the `get_base_args()` method. Locate this line within that method

`ansible_version = _get_ansible_version(ansible_inventory_path[:-len('-inventory')])`

Comment it out and replace it with 

`ansible_version = "2.11.1"`