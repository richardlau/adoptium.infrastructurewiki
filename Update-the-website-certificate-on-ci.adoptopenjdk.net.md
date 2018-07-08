Login to `ci.adoptopenjdk.net` as `root` and run the following commands:

1. `cd /opt/letsencrypt`
2. `./letsencrypt-auto --config /etc/letsencrypt/configs/ci.adoptopenjdk.net.conf certonly`
3. `service nginx restart`

See also:
`/etc/cron.monthly/renew-ssl-ci.adoptopenjdk.net.sh`