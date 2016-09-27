# Vagrant Local Development Environment
* Docs available at https://github.com/cuweb/docs-developer/wiki/Ansible---Vagrant

# For SSL
* SSH into the vagrant `vagrant ssh`
* Command line `su`
* Password is `vagrant`
* `openssl genrsa -des3 -out server.key 2048`
* `openssl rsa -in server.key -out server.key.insecure`
* `mv server.key server.key.secure`
* `mv server.key.insecure server.key`
* `openssl req -new -key server.key -out server.csr`
* `openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt`
* `sudo cp server.crt /etc/ssl/certs`
* `sudo cp server.key /etc/ssl/private`
* Run `vagrant provision`