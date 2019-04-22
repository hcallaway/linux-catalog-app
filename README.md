# Linux Configuration Project
## Udacity Full Stack NanoDegree Project

## Information
1. IP Address: 34.214.66.239
2. 34.214.66.239.xip.io


## Steps taken for config
### Update Server
```sudo apt update && sudo apt upgrade``` to update all packages on new server

### Update time to UTC
1. ```sudo timedatectl set-timezone UTC```

### Firewall Setup
1. ```ufw default deny incoming```
2. ```ufw default allow outgoing```
3. ```ufw allow ssh```
4. ```ufw allow 2200/tcp```
5. ```ufw allow www```
6. ```ufw allow ntp```
7. ```sudo ufw show added``` to confirm ports
8. ```sudo ufw enable``` to enable these changes

### Install Packages Needed for App
```
sudo apt install python3-pip
sudo apt install python3-flask
sudo apt install apache2
sudo apt install libapache2-mod-wsgi-py3
sudo apt install python3-sqlalchemy python3-oauth2client
sudo apt install python3-httplib2 python3-requests
```
### Confirm Git is installed
confirmed git is installed with ```git --version```

If not installed, run ```sudo apt install git```

### Clone Repository
16. in /var/www ```sudo git clone git@github.com:hcallaway/linux-catalog-app.git catalog```

### Create .wsgi file
In /var/www/catalog

```sudo touch catalog.wsgi && sudo nano catalog.wsgi```

Insert and save:
```
import sys

sys.path.insert(0, "/var/www/catalog/")
from catalog import app as application
application.secret_key = ```super_secret_key```
```

### Create and config new virtual host
```cd /etc/apache2/sites-available && sudo nano catalog.conf```

Insert and save:
```
    <VirtualHost *>
        ServerName Catalog.com
        WSGIScriptAlias / /var/www/catalog/catalog.wsgi
        WSGIDaemonProcess catalog
        WSGIProcessGroup catalog
        <Directory />var/www/catalog/catalog>
            WSGIProcessGroup catalog
            WSGIApplicationGroup %{GLOBAL}
            Require all granted
        </Directory>
    </VirtualHost>
```
```
sudo a2dissite 000-default.conf
sudo a2ensite catalog.conf
sudo service apache2 reload
```

### Create & Configure Grader User
1. ```sudo  adduser grader```, fullname = Udacity Grader, pass: grader
2. ```sudo touch /etc/sudoers.d/grader && sudo nano /etc/sudoers.d/grader```
3. Add ```grader ALL=(ALL) NOPASSWD:ALL``` to the file, save and exit
4. Generate SSH key-pair on local machine
5. ```ssh-keygen``` store in new location in .ssh file (for easy access)
6. copy public key to use
7. add public key to server
as ubuntu user: ```cd /home/grader && sudo mkdir .ssh```
8. ```sudo touch .ssh/authorized_keys```
9. ```sudo nano .ssh/authorized_keys``` and paste in local generated key

### Install and Configure PostgreSQL
1. ```sudo apt install postgresql```
2. ```confirm remote connections aren't allowed```
3. ```sudo su - postgres```
4. ```psql```
5. ```CREATE DATABSE catalog;```
6. ```CREATE USER catalog;```
7. ```ALTER ROLE catalog WITH PASSWORD 'catalog';```
8. ```GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;```
9. ```\q```
10. ```exit```


### Helpful Resources
Massive thanks to JungleBadger for his debugging file [here](https://github.com/jungleBadger/-nanodegree-linux-server-troubleshoot/tree/master/python3%2Bvenv%2Bwsgi) and his setup guide [here](https://github.com/jungleBadger/-nanodegree-linux-server/blob/master/README.md), this two items were critical for me getting past roadblocks

Also referenced:
* https://alonavarshal.com/blog/flask-on-lightsail-aws/
* https://github.com/SteveWooding/fullstack-nanodegree-linux-server-config
* [Switching to Postgres](https://docs.sqlalchemy.org/en/13/core/engines.html#postgresql)
* The random answer in Udacity Knowledge that I can't find that pointed the way toward the ```tails``` command for the Apache logs, infinite time and lifesaver