In the event that Jenkins crashes, use the following steps to bring it back online:

1. `ssh root@ci.adoptopenjdk.net` (you'll need to get the password from LastPass if you're in the approved list)
2. `service jenkins start`

If the service doesn't start and there's no obvious reason in _/var/log/jenkins/jenkins.log_ then you can try

1. `nohup /usr/bin/daemon --name=jenkins --inherit --env=JENKINS_HOME=/home/jenkins/.jenkins --output=/var/log/jenkins/jenkins.log --pidfile=/var/run/jenkins/jenkins.pid -- /usr/bin/java -Djava.awt.headless=true -jar /usr/share/jenkins/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080`

Don't forget to exit your shell!