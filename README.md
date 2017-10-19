This is the 5th and final project for Udacity Full Stack Nanodegree
# Resources 
- Used tutorial from https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart
# linux-server
Udacity Full Stack Developer Linux Server Project 
- IP ADDRESS: 54.152.137.233
- SSH PORT: 2200
- URL: HTTP://54.152.137.233
# Change the SSH Port from 22 to 2200
1. In terminal: 
    - $ sudo vim /etc.ssh/sshd_config (where it says Port 22, change to Port 2200)
    - Reload ssh: $ sudo service ssh restart 
# Configure Uncomplicated Firewall to only allow connections for SSH(port 2200), HTTP(port80), and NTP(port 123)
1. In terminal:
    - $ sudo ufw allow 2200/tcp
    - $ sudo ufw allow 80/tcp
    - $ sudo ufw allow 123/upd
    - $ sudo ufw enable 
# Create new user (grader)
1. sudo adduser grader
2. vim /etc/sudoers
3. touch /etc/sudoers.d/grader
4. vim /etc/sudoers.d/grader
- edit file to look like: grader ALL=(ALL:ALL)ALL
- save and quit 
# Create SSH key for user grader
1. ganerate key pair using ssh-keygen on local machine and save private key on local machine in ~/.ssh
2. Add public key to grader by next steps in vm:
    - $ su - grader
    - $ mkdir .ssh
    - $ touch .ssh/authorized_keys
    - $ vim .ssh/authorized_keys 
3. Copy the public key and paste it into .ssh/authorized_keys
4. save and quit 
5. In terminal: 
    - $ chmod 700 .ssh
    - $ chmod 644 .ssh/authorized_keys
6. Restart ssh:
    - $ sudo service ssh restart
7. Login in terminal as new user: ssh -i (privateKeyFile) grader@54.152.137.233 -p 2200
# Update all currently installed packages
- sudo apt-get update
- sudo apt-get upgrade
# Configure local time zone
1. To check if it is UTC:
    - $ sudo dpkg-reconfigure tzdata
2. Change to UTC
# Install and configure Apache to serve mod_wsgi application
1. Install Apache in terminal:
    - $ sudo apt-get install apache2
2. Install mod_wsgi:
    - $ sudo apt-get install python-setuptools libapache2-mod-wsgi
3. Restart apache server:
    - $ sudo service apache2 restart
# Install and configure PostgreSQL
1. Install PostgreSQL
    - $ sudo apt-get install postgresql
2. Check for remote connections:
    - $ sudo vim /etc/postgresql/9.5/main/pg_hba.conf
3. Login 
    - sudo su- postgres
4. Get into Postgres shell
    - $ psql
5. Create new database and user both named catalog
    - postgres# CREATE DATABASE catalog;
    - postgres# CREATE USER catalog;
    - postgres# ALTER ROLE catalog WITH PASSWORD 'password';
6. Grant user 'catalog' access to database 'catalog'
    - postgres# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
7. quit: postgres# \q and exit: exit 
# Clone and set up Items Catalog project 
1. Install git: 
    - $ sudo apt-get install git
2. cd in /var/www directory
    - $ cd /var/www 
3. Create our application directory 
    - $ sudo mkdir FlaskApp
4. Move inside this directory
    - $ cd FlaskApp
5. Clone Items Catalog project into this folder 
    - git clone https://github.com/gavmcnamara/music-catalog-api.git
6. Rename project:
        - $ sudo mv ./music-catalog-api ./ItemsApp
7. Move into ItemsApp
        - $ cd ItemsApp
8. Rename flask_project.py to __init__.py
9. Edit tables.py and bandsandgenres.py to have new database:
        - create_engine('postgresql://catalog:password@localhost/catalog')
10. Install pip:
        - $ sudo spt-get install python-pip
11. Use pip to install requirements:
        - pip3 install --upgrade pip
        - pip3 install flask packaging oauth2client redis passlib flask-httpauth
         -pip3 install sqlalchemy flask-sqlalchemy psycopg2 bleach requests
12. Install psycopg2:
        - $ sudo apt-get -qqy install postgresql python-psycopg2
13. Create database schema:
        - sudo python tables.py
# Set up Server so it functions correctly
1. Create FlaskApp.conf and edit:
        - $ sudo nano /etc/apache2/sites-available/FlaskApp.conf
2. Add the following to this new file:

``` <VirtualHost *:80>
 	ServerName 54.152.137.233
 	ServerAdmin ubuntu@54.152.137.233
 	WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
 	<Directory /var/www/FlaskApp/ItemsApp/>
 		Order allow,deny
 		Allow from all
 	</Directory>
 	Alias /static /var/www/FlaskApp/ItemsApp/static
 	<Directory /var/www/FlaskApp/ItemsApp/static/>
 		Order allow,deny
 		Allow from all
 	</Directory>
 	ErrorLog ${APACHE_LOG_DIR}/error.log
 	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
 </VirtualHost> 
 ```

3. Save and quit  
4. Enable your virtual host:
        - $ sudo a2ensite FlaskApp
# Create your .wsgi file 
1. Go back to directory FlaskApp and create flaskapp.wsgi
        - $ cd /var/www/FlaskApp
        - $ sudo nano flaskapp.wsgi
2. Add to new file:

``` #!/usr/bin/python
 import sys
 import logging
 logging.basicConfig(stream=sys.stderr)
 sys.path.insert(0,"/var/www/FlaskApp/")

 from ItemsApp import app as application
 application.secret_key = 'Add your secret key'
```
3. Save and quit
# Restart apache server
- $ sudo service apache2 restart 
