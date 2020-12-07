# Server setup:

As new `bastillion` user:
```
wget -O -  https://github.com/bastillion-io/Bastillion/releases/download/v3.09.00/bastillion-jetty-v3.09_00.tar.gz | tar xpzf -
perl -p -i -e "s/forceUserKeyGeneration=true/forceUserKeyGeneration=false/"	./Bastillion-jetty/jetty/bastillion/WEB-INF/classes/BastillionConfig.properties
perl -p -i -e "s/resetApplicationSSHKey=.*/resetApplicationSSHKey=true/"	./Bastillion-jetty/jetty/bastillion/WEB-INF/classes/BastillionConfig.properties
perl -p -i -e "s,privateKey=.*/privateKey=$HOME/.ssh/id_rsa,"			./Bastillion-jetty/jetty/bastillion/WEB-INF/classes/BastillionConfig.properties
perl -p -i -e "s,publicKey=.*,privateKey=$HOME/.ssh/id_rsa.pub,"		./Bastillion-jetty/jetty/bastillion/WEB-INF/classes/BastillionConfig.properties
perl -p -i -e "s/defaultSSHPassphrase=.*/defaultSSHPassphrase=/"		./Bastillion-jetty/jetty/bastillion/WEB-INF/classes/BastillionConfig.properties
[ ! -r "$HOME/.ssh/id_rsa" ] && ssh-keygen -b 8192 -C "bastillion@adoptopenjdk" -N "" -f $HOME/.ssh/id_rsa
wget -O - https://github.com/AdoptOpenJDK/openjdk11-binaries/releases/download/jdk-11.0.6%2B10_openj9-0.18.1/OpenJDK11U-jdk_x64_linux_openj9_11.0.6_10_openj9-0.18.1.tar.gz | tar xpfz -
```
Once setup with the above commands start it with:
- `export PATH=$PWD/jdk-11.0.6+10/bin:$PATH; cd Bastillion-jetty; ./startBastillion.sh`

Once started, you can log in to the web UI as the `admin` user

# Setting up via the web UI

The "Manage" menu is the important one in the UI. Under that menu you'll find the following things that can be defined:

## Systems

Systems are the things which Bastillion manages. In our case it is a username and host pair, so for example the `root` and `jenkins` users for each host are defined as individual systems.

## Profiles

A profile is the linkage between systems and users. You can think of them like groups which contain users and systems and allows the users in the profile to access the systems that are in it.

## Users

Each person who has acccess to the system has a user ID on bastillion. In order to add/remove their keys, the user has to log in as their own account (it cannot be done by an admin). Their keys have to be defined on a per-profile basis so if a user has access to multiple profiles then they will need define the key multiple times. If you find that someone can access some systems and not others despite being in the right profile, it's worth checking that they have a key for the profile that isn't working.

## Which profiles do we have?

At the time of writing we use the following profiles:

Profile name | Usage
--|--
jenkins | Access to the jenkins user on most machines
jenkins-secret | jenkins user on restricted machines (e.g. any that have special access keys)
root | General root access for the infrastructure teams (excludes AIX+Linux/s390x)
root-secret | root user on restricted machiens
root-ibmaix | root access to the AIX machines
root-marist | root access to the Marist Linux/s390x systems

# Directly adding entries to the underlying h2 database

If you are just adding one system then the web UI is perfectly adequate, but if you are bulk adding multiple machines in can be tedious. You can access the underlying database directly. This section will tell you what you need to know to do that.

To connect to the database, shutdown the server then run a command such as this (version numbers may vary, and obviously `myDBpassword` is not the real one :-)

`~bastillion/jdk-11.0.6+10/bin/java -cp /home/bastillion/Bastillion-jetty/jetty/bastillion/WEB-INF/lib/h2-1.4.199.jar org.h2.tools.Shell -driver org.h2.Driver -url 'jdbc:h2:/home/bastillion/Bastillion-jetty/jetty/bastillion/WEB-INF/classes/keydb/bastillion;CIPHER=AES;' -user bastillion -password "filepwd myDBpassword"`

Each profile has an ID associated with it - you can see them by looking at the `profiles` table:

```
sql> select * from profiles;
ID | NM             | DESC
1  | jenkins-only   | Access to the jenkins accounts on UNIX-based systems which have no secret keys
2  | jenkins-secret | Access to the jenkins account on systems which have secret keys
3  | root           | root access to most machines
4  | root-secret    | root access to systems that have secret keys on them
5  | root-marist    | root access to marist machines (which have their own private keys which we do not want to overwrite
6  | admin-ibmaix   | IBM AIX systems with their key included
(6 rows, 3 ms)
sql> 
```
When initially loading the database I used the following shell script:
```
#!/bin/bash -x
[ $# -lt 3 ] && echo $0 display_name ip profile && exit 1
case $3 in
           jenkins) PROFILE_ID=1;;
    jenkins-secret) PROFILE_ID=2;;
              root) PROFILE_ID=3;;
       root-secret) PROFILE_ID=4;;
       root-marist) PROFILE_ID=5;;
       root-ibmaix) PROFILE_ID=6;;
                 *) echo Invalid profile name - should be one of: jenkins-only jenkins-secrert root root-secret root-secret admin-ibmaix && exit 2;;
esac

echo "insert into system values (default, '$1', 'root', '$2', 22, '~/.ssh/authorized_keys', default);"
echo "insert into system_map values ($PROFILE_ID, select id from system where display_nm='$1');"
```
This allows the execution of commands such as the following to add machines:
`bash ./import.sql test-ibm-aix71-ppc64-1        129.33.196.209   root-ibmaix`
