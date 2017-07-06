# udacity-server-config

Server IP: 46.101.142.154 
URL: http://46.101.142.154/

## Make sure all installed software is up to date
1. Run sudo apt-get update to package list
2. Run sudo apt-get upgrade to update all the installed packages 

## Creating user
1. sudo adduser grader
2. create /etc/sudoers.d/grader to configure sudo access for newly created user (another option is to use gpasswd -a grader sudo command)

## Adding public key authentication 
1. generate new key-pair using ssh-keygen
2. copy public key using ssh-copy-id grader@46.101.142.154 command

## Disabling root login
1. Change PermitRootLogin yes to PermitRootLogin no in /etc/ssh/sshd_config file
2. Change PasswordAuthentication yes to PasswordAuthentication no to disable password based auth
3. Reload SSH by service ssh restart

## Changing SSH port
1. Change Port to 2200 in /etc/ssh/sshd_config

## Firewall config
1. Run sudo ufw allow 2200 to allow SSH connections
2. Run sudo ufw allow http to allow HTTP connections
3. Run sudo ufw allow ntp to allow NTP connections
4. Enable firewall by running sudo ufw enable

## Install python and other needed software
Run sudo apt-get install python-pip python-dev libpq-dev postgresql postgresql-contrib apache2 libapache2-mod-wsgi

## Project setup 
1. Clone catalog project into /var/www/udacity-catalog
2. Database setup
- sudo -u postgres psql
- CREATE USER catalog WITH PASSWORD ‘XXX’; 
- GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
3. Modify apache config (/etc/apache2/sites-enabled/000-default.conf) as follows:
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/udacity-catalog

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        WSGIScriptAlias / /var/www/udacity-catalog/catalog.wsgi
</VirtualHost>
```

4. Create catalog.wsgi file as follows
```
#!/var/www/udacity-catalog/venv/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)

activate_this = '/var/www/udacity-catalog/venv/bin/activate_this.py'
execfile(activate_this, dict(__file__=activate_this))

sys.path.insert(0, "/var/www/udacity-catalog/")

from project import app as application
application.secret_key = "udacity-catalog"
```


5. Setup venv and install the requirements  
6. Change database connection from sqlite to posgresql
7. Restart apache by running service apache2 restart
