# Vagrant Local Development Environment
* Docs available at https://github.com/cuweb/docs-developer/wiki/Ansible---Vagrant

# Before you run the `vagrant up`, do this in terminal:
* `vagrant plugin install vagrant-hostmanager`

# Temporarily remove
* go to `/ansible/roles/vhost-db/templates/site_vhost.j2` and remove the following. Once the vagrant up process has been completed, undo the changes

```
<VirtualHost *:443>
    SSLEngine on
    SSLCertificateFile    /etc/ssl/certs/server.crt
    SSLCertificateKeyFile /etc/ssl/private/server.key

    ServerAdmin web@carleton.ca
    ServerName {{ item.servername }}
    ServerAlias {{ item.servername }}
    DocumentRoot /var/www/{{ item.documentrootdir }}
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    <Directory /var/www/{{ item.documentrootdir }}>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order allow,deny
        allow from all
    </Directory>
</VirtualHost>
```

* go to `/ansible/roles/apache/tasks/main.yml` and remove the following. Once the vagrant up process has been completed, undo the changes

```
- name: Enable SSL
  sudo: true
  shell: sudo a2enmod ssl
  notify: restart apache
```

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