In the event that Jenkins crashes, use the following steps to bring it back online:

1. `ssh root@ci.adoptopenjdk.net` (you'll need to get the password from LastPass if you're in the approved list)
2. `service jenkins restart`

If the service doesn't start and there's no obvious reason in `/var/log/jenkins/jenkins.log` then you can try

1. `nohup /usr/bin/daemon --name=jenkins --inherit --env=JENKINS_HOME=/home/jenkins/.jenkins --output=/var/log/jenkins/jenkins.log --pidfile=/var/run/jenkins/jenkins.pid -- /usr/bin/java -Djava.awt.headless=true -jar /usr/share/jenkins/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080`

Don't forget to exit your shell!

## When Jenkins restart fails

Look at _/var/log/jenkins/jenkins.log_

Most of the time it's because someone accidentally started Jenkins as root (e.g. not via the service) and so _/var/cache/jenkins/_ is owned by `root:root` and you'll need to recursively change ownership to `jenkins:jenkins` e.g.

`chown -R jenkins:jenkins /var/cache/jenkins/`