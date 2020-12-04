# Ubuntu server setup:

- apt install git docker.io python3-pip libffi-dev 
- useradd -g docker -m ansible

As the new ansible user:

- git clone https://github.com/ansible/awx
- openssl req -x509  -nodes -days 90  -newkey rsa:4096  -keyout server.key -out server.crt  -subj "/C=GB/ST=UK/L=London/O=AdoptOpenJDK/CN=awx.adoptopenjdk.net"
- Edit `awx/installer/install/inventory` and update `ssl_certificate` and `ssl_certificate_key` to the full path to the two files created from the openssl command above. Also change `admin_password` to something other thatn `password` (but probably not somethign with `!` in it)
- export PATH=$HOME/.local/bin:$PATH
- pip3 install docker-compose ansible
- cd awx/installer
- ansible-playbook -i inventory install.yml
- docker logs -f awx_task

# Once ansible is running, connect to it on the standard SSL port (443)

