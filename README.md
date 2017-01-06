# Vagrant Local Development Environment
* Docs for dev tool installation available at https://github.com/cuweb/docs-developer/wiki/Mac-Dev-Tool-Setup

# Main Setup

## Host Manager
Go to terminal and run
```
vagrant plugin install vagrant-hostmanager
```

## Vagrantfile Config
* Duplicate `Vagrantfile_example` and name it `Vagrantfile`
* Open `Vagrantfile` and add new hosts on line 16, `node.hostmanager.aliases`

## Playbook Config
* Inside the `/ansible` directory, duplicate `playbook_example.yml` and name it `playbook.yml`
* Open `playbook.yml` and add new hosts using the following example:

```
- servername: "cu-local.carleton.ca"
  documentrootdir: "cu-local"
  dbname: "cu-local"
  errorlog: "cu-local"
  customlog: "cu-local"
```

# Before Finalization

## Temporarily remove
The following items are used to enable SSL on the vagrant box, but should be temporarily removed before getting setup.

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

# Finalize the Vagrant Setup
* In terminal run `vagrant up`

# Setup SSL in your Vagrant
Run each of the following items in terminal
* Do not forget to revert back the `/ansible/roles/vhost-db/templates/site_vhost.j2`, and `/ansible/roles/apache/tasks/main.yml` files before moving onto the SSL setup
* Password for `su` is `vagrant`
```
vagrant ssh
su
vagrant
openssl genrsa -des3 -out server.key 2048
openssl rsa -in server.key -out server.key.insecure
mv server.key server.key.secure
mv server.key.insecure server.key
openssl req -new -key server.key -out server.csr
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
sudo cp server.crt /etc/ssl/certs
sudo cp server.key /etc/ssl/private
vagrant provision
```
# Additional Information

## Adding New Hosts
* Suspend the vagrant with `vagrant suspend`
* Add entries for new sites to the `Vagrantfile` and to the `playbook.yml`
* Resume the vagrant with `vagrant resume`
* Once the machine boots, provision with `vagrant provision`

## URLs and Passwords
* phpMyAdmin is at: 192.168.33.33/phpMyAdmin
* SSH user is `vagrant` and password is `vagrant`
* MySQL user is `wordpress` and password is `wordpress`
* Web path on the server is `/var/wwww/` and is mapped to the `sites` folder locally (will be created on startup)

## Accessing Logs
* You can SSH in using `vagrant ssh`
* To view the error log, run `vagrant ssh` then once logged in you need to switch to root by typing `su` (password is `vagrant`) then `cd /var/log/apache2`
* `tail -f error.log`