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

Once the above has been completed, go to `/ansible/roles/vhost-db/templates/site_vhost.j2` and add this at the bottom:

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