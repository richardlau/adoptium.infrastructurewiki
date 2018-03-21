### How to start server.js on api.adoptopenjdk.net

_Note: Direct ssh access is blocked (or limited) to this host._

Part 1)

Log into `keybox` and start a `Composite SSH Terms` connection to `api.adoptopenjdk.net` by doing the following:

- Open the link to [Keybox](https://keybox.adoptopenjdk.net:8443)
- Click on `Secure Shell` then on `Composite SSH Terms`
- Select `api.adoptopenjdk.net root` then click on `Create SSH Terminal`



Part 2)

```
How to restart the api site:
# su - jenkins
# export PRODUCTION=true && /usr/local/bin/forever start /home/jenkins/openjdk-api/server.js 
```
