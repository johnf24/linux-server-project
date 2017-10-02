# Linux Server Configuration Project
This is a baseline installation of an Linux distribution on a virtual machine. It hosts web applications to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

## Server Details
Public IP Address: 18.221.163.185 <br />
SSH Port: 2200 <br />
URL: http://ec2-18-221-163-185.us-east-2.compute.amazonaws.com <br />
Software Installation: Ubuntu 14.4.5, Python 2.7.6, Apache2 2.4.7, PostgreSQL 9.3.18, Flask 0.12.2, SQLAlchemy 1.2, mod_wsgi package, Git <br />
Configurations Made: '' <br />
Resources: [Apache Documentation](https://httpd.apache.org/docs/2.2/configuring.html)

 [PostgreSQL Documentation] (https://www.postgresql.org/) [Ubuntu Documentation] (https://help.ubuntu.com/community/Sudoers) [Digital Ocean] (https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps), (https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04), (https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps), (https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server) [Stack Overflow] (https://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible).

## Installation

### Install and Update Software
1. Navigate to the directory inside your vagrant environment.
2. Update package lists by running command 'sudo apt-get update'.
3. Upgrade package lists by running command 'sudo apt-get upgrade'.
(Optional) Install Finger by running command 'sudo apt-get install finger'.
4. Install Git by running command 'sudo apt-get install git'.
5. Install Python Package by running command 'sudo apt-get install python-pip'.
6. Install Python Dev by running command 'sudo apt-get install python-dev'.
7. Install Apache HTTP Server by running command 'sudo apt-get install apache2'.
8. Configure Apache for the application handler mod_wsgi by running command 'sudo apt-get install libapache2-mod-wsgi'. Then enable with command 'sudo a2enmod wsgi'.
9. Install PostgreSQL by runnning command 'sudo apt-get install postgresql'. Then install modules with command 'sudo apt-get install postgresql-contrib'.
10. Install Python Psycopg by running command 'sudo apt-get install python-psycopg2'.

### Create User
1. Create a new user named grader by running command 'sudo adduser grader'. Then create a secure password.
(Optional) Lookup information by running command 'finger grader'.
2. Edit configuration file by running command 'sudo nano /etc/ssh/sshd_config'. Then change 'Port 22' to 'Port 2200' and save file.
3. Attempt to connect as the new user by running command 'ssh grader@18.221.163.185 -p 2200'. Then press 'yes' and enter password.
4. Log out by entering 'ctrl d'.

### Give User Sudo Access
1. Logged in as the Superuser on the local server edit sudoers file by running command 'sudo nano /etc/sudoers.d/grader'.
2. Then paste content and save file.

`grader ALL=(ALL:ALL) ALL`

### Create and Install Key Pair
1. Still logged in as the Superuser on the local server generate SSH key by running command 'ssh-keygen'.
2. The file and path for the key should be '/home/ubuntu/.ssh/grader_key', then press enter.
3. (Optinal) Create a passphrase.
4. Log back in as Grader on the remote server and create a directory by running command 'mkdir .ssh'.
5. Create the new file Authorized Keys by running command 'touch .ssh/authorized_keys'.
6. Log out by entering 'ctrl d'.
7. As the Superuser on the local server, view file contents of Authorized Keys file by running command 'cat .ssh/grader_key.pub' and copy contents.
8. Log in as Grader on the remote server and edit Authorized Keys file by runnning command 'nano .ssh/authorized_keys' and then paste contents and save file.
9. Set permissions by running command 'chmod 700 .ssh' and then 'chmod 644 .ssh/authorized_keys'.
10. Log out by entering 'ctrl d'.
11. Now log in as Grader on the remote server using the key pair by running command 'ssh grader@18.221.163.185 -p 2200 -i ~/.ssh/grader_key'.

### Force Key Authentication
1. Logged in as Grader on the remote server edit the configuration file again with command 'sudo nano /etc/ssh/sshd_config'.
2. Change 'PasswordAuthentication' from yes to no.
3. Change 'PermitRootLogin without-password' to 'PermitRootLogin no' and save file.
4. Restart service by running command 'sudo service ssh restart'.
5. Log out by entering 'ctrl d'.
6. Log back in as Grader on the remote server by running command 'ssh grader@18.221.163.185 -p 2200 -i ~/.ssh/grader_key'.

### Configure UFW Firewall
1. Logged in as Grader deny incoming conncetions by running command 'sudo ufw default deny incoming'.
2. Allow outgoing connections by running command 'sudo ufw default allow outgoing'.
3. Allow ssh on port 2200 by running command 'sudo ufw allow 2200/tcp'.
4. Allow http server on port 80 by running command 'sudo ufw allow 80/tcp'.
5. Allow ntp on port 123 by running command 'sudo ufw allow 123/udp'.
6. Check added connections by running command 'sudo ufw show added'. Then enable the firewall with command 'sudo ufw enable'.
7. Check firewall status by running command 'sudo ufw status'.

### Create Flask App
1. Logged in as Grader on the remote server navigate to directory var by running command 'cd /var/www'.
2. Make new directory by running command 'sudo mkdir catalog' and navigate to the directory by running command 'cd catalog'. Then make another new directory with command 'sudo mkdir catalog' and navigate to the directory with command 'cd catalog' and make two more directories with command 'sudo mkdir static templates'.
3. In the catalog directory install Virtualenv by running command 'sudo pip install virtualenv'. Then name the envoirment with command 'sudo virtualenv venv' and activate virtual enviroment with command 'source venv/bin/activate'.
4. Install Flask in virtual enviroment by running command 'sudo pip install Flask'.
5. Install httplib module in virtual enviroment by running command 'sudo pip install httplib2'.
6. Install Requests module in virtual enviroment by running command 'sudo pip install requests'.
7. Install SQLAlchemy in virtual enviroment by running command 'sudo pip install sqlalchemy'.
8. Install OAuth client in virtual enviroment by running command 'sudo pip install --upgrade oauth2client'.
9. Create Flask file by running command 'sudo nano __init__.py'.
10. Paste content and save file.

`from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Hello World!"
if __name__ == "__main__":
    app.run()`

11. Run Flask file with command 'sudo __init__.py'.
12. Deactive Virtualenv by running command 'deactivate'.
13. Create virtual enviroment configuration file by running command 'sudo nano /etc/apache2/sites-available/catalog.conf'.
14. Paste content and save file.

`<VirtualHost *:80>
     ServerName 18.221.163.185
     ServerAdmin admin@18.221.163.185
     ServerAlias ec2-18-221-163-185.us-east-2.compute.amazonaws.com
     WSGIScriptAlias / /var/www/catalog/catalog.wsgi
     <Directory /var/www/catalog/catalog/>
         Order allow,deny
         Allow from all
     </Directory>
     Alias /static /var/www/catalog/catalog/static
     <Directory /var/www/catalog/catalog/static/>
         Order allow,deny
         Allow from all
     </Directory>
     ErrorLog ${APACHE_LOG_DIR}/error.log
     LogLevel warn
     CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>`

15. Enable virtual enviroment by running command 'sudo a2ensite catalog'.
16. Navigate to Catalog directory with command 'cd /var/www/catalog' and create a WSGI file by running command 'sudo nano catalog.wsgi'.
17. Paste content and save file.

`#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")
from catalog import app as application
application.secret_key = 'secret`

18. Restart Apache again by running command 'sudo service apache2 restart'.

### Git Repository
1. Navigate inside Catalog directory by running command 'cd /var/www/catalog/catalog/'. Then clone Git repository with command 'sudo git clone https://github.com/johnf24/item-catalog-project'.
2. Move files into catalog folder by running command 'mv /var/www/catalog/catalog/item-catalog-project/* /var/www/catalog/catalog/'.
3. Delete item-catalog-project folder with command 'sudo rm -r item-catalog-project'.
4. Copy and move application by running command 'sudo mv app.py __init__.py'.

### Configure PostgreSQL
1. Change to PostgreSQL to the user with command 'sudo su - postgres' and connect to the database with command 'psql'. 
2. Create another new user and password for Catalog by entering query 'CREATE USER catalog WITH PASSWORD 'dbpassword';'.
3. Change catalog user role by entering query 'ALTER USER catalog CREATEDB;'.
4. Create database by entering query 'CREATE DATABASE catalog WITH OWNER catalog;'. Then connect to database with query '\c catalog'.
5. Revoke rights by entering query 'REVOKE ALL ON SCHEMA public FROM public;'.
6. Change permissions by entering query 'GRANT ALL ON SCHEMA public TO catalog;'.
7. Quit PostgreSQL by runnnging command '\q' and 'exit'.
8. Edit application by running command 'sudo nano __init__.py'.
9. Change line 'engine = create_engine('sqlite:///groceryitemswithusers.db')' to 'engine = create_engine('postgresql://catalog:dbpassword@localhost/catalog')'.
10. Then update client sercrets path from 'client_secrets.json' to '/var/www/catalog/catalog/client_secrets.json' and save.
11. Edit database by running command 'sudo nano database_setup.py'.
12. Change line 'engine = create_engine('sqlite:///groceryitemswithusers.db')' to 'engine = create_engine('postgresql://catalog:dbpassword@localhost/catalog')' and save.
13. Setup database schema by running command 'python database_setup.py'.

### Update Google OAuth Log Ins
1. Navigate to the Google Developers Console and add your Hostname and IP Address to authorized javaScript origins and authorized redirect URIs. Then add Hostname and IP Address to client_secrets.json file.

### Configure Timezone
1. Make sure timezone is set to UTC by running command 'date'. If it is not on UTC change timezone with command 'sudo timedatectl set-timezone UTC'.

### Run Application
1. Navigate to "http://ec2-18-221-163-185.us-east-2.compute.amazonaws.com"

 in your browser.

### Troubleshooting
1. Check for errors by running command 'sudo tail -10 /var/log/apache2/error.log'.