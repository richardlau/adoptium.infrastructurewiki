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
- To directly access the bastillion database: `~bastillion/jdk-11.0.6+10/bin/java -cp /home/bastillion/Bastillion-jetty/jetty/bastillion/WEB-INF/lib/h2-1.4.199.jar org.h2.tools.Shell -driver org.h2.Driver -url 'jdbc:h2:/home/bastillion/Bastillion-jetty/jetty/bastillion/WEB-INF/classes/keydb/bastillion;CIPHER=AES;' -user bastillion -password "filepwd myDBpassword"`

Log in to the web UI as the `admin` user:

# Directly adding entries to the underlying h2 database

# Setting up via the web UI
