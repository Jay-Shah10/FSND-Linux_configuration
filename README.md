# FSND-Linux_configuration
Linux box configuration.

## Overview
This is the fifth project for Udacity Full Stack Development. The main purpose of this is to configure the linux box so that we can host the web application. This will walk you through how to configure you linux box in order to host a Flask app.

## Requirements
```
* AWS developer account
* Lighsail instance
```

## How to
When you first connect to the server. update and upgrade all linux packages.
```
sudo apt-get update
sudo apt-get upgrade
```

### Step one:  setting up ports.
Navigate to the following link: https://console.aws.amazon.com and set up your account.  
We will be using the lightsail instance. Click on "Build Using Virutal Service - with Light Sail."  
Click on "linux", "OS Only", and select your version of Ubuntu (I have chosen - 16.x).
Once your instance is set up and running, connnect to your VM. This will open a window and you now have a terminal 
for you to interact with.  

You will now configure a port for the VM so that it is open.  
Navigate to the lightsail instance on console.aws page. - click on the three dots and click on "manage."
Navigate to the "Network" section and click on "Add" Type the following in.
```
custom | port: 2200
```

### Step Two: configure firewall.
Here we are going to configure the firewall and add a user.  
Make sure that the firewall is disable before we start.
Make the following changes: 
```
sudo ufw allow ssh
sudo ufw allow www
sudo ufw allow 2200/tcp
sudo ufw allow 123/udp
sudo ufw allow 80/tcp
```
Makesure these are allowed before enabling ufw.
To eanble the fire wall and to check the status.
```
sudo ufw enable
sudo ufw status
```

status - will show which rule is allowed. 

We will also make a change to a file to allow this port. 
```
sudo vim /etc/ssh/shh_config

add in 
Port 2200
under Port 22
```

The last thing we want to do it configure the time zone.
```
sudo dpkg-reconfigure tzdata
```


### Step Three: creating user.
We are going to create a user called grader and give them sudo rights.
```
sudo adduser grader
sudo adduser grader sudo
```
we also need to add the rights to sudoers file. 
```
sudo visudo
```
add in the following line under "root ALL=(ALL:ALL) ALL"
```
grader ALL=(ALL:ALL) ALL
```
To check if grader has sudo rights: 
```
sudo su - grader
sudo whoami
```

you can exit user using 
```
exit
```

### Step Four: ssh key and remote connection.
Now to generate a key that will be needed in order to connect to server remotely.
This will be done using a tool called ssh-keygen.
This will be first genreated on you local vagrant machine.
```
ssh-keygen
```
make a directory called .ssh/linuxcourse
```
mkdir .ssh
cd .ssh
mkdir linuxcourse
```
you should have a private and public key.
open the public key file.
```
cat linuxcourse.pub
```
we will need this key to be pasted in the lighsail server.
switch to user grader
```
sudo su - grader
```
Create a folder called .ssh
```
sudo mkdir .ssh
```
create a file that will hold the key. 
```
sudo vim .ssh/authorized_keys
```
paste the public key that was created and paste it there.
Now to make a connection to the server remotely
```
ssh -i ~/.ssh/linuxcourse grader@34.200.229.181
```
static public IP = 34.200.229.181

### Step Five: Configuring
<strong>Install and configure Apache and mod_wsgi.</strong>  
Install apache ```sudo apt-get install apache2```  
Install mod_wsgi ```sudo apt-get install python-setuptools libapache2-mod-wsgi```  
Restart apache ```sudo service apache restart```  

Install and Configure postgresql. 
```
sudo apt-get install postgresql
```
login in as user "postgres'
```
sudo su - postgres
```
Get into postgresql api: ```psql```
Create a database and a user. 
```
postgres=# CREATE DATABASE catalog;
postgres=# CREATE USER catalog;
```
Set a password for user catalog
```
postgres=# ALTER ROLE catalog WITH PASSWORD 'password';
```
Give user permission to catalog application database.
```
postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
```
Exit out of the api and exit user. 
```
postgres=# \q
exit
```

<strong>Install Git and configure Postgresql</strong>
```
sudo apt-get install git
```
Navigate to dir: ```cd /var/www/```  
Create a folder called "FlaskApp"  and movie into the dir ```cd FlaskApp```.  
Clone in your repo
```
git clone <repo link>
```
once you are here rename the repo folder to "FlaskApp"
```
sudo mv ./<repo_name> ./FlaskApp
cd FlaskApp
```
Rename application.py to ```__init__.py```
``` sudo mv application.py __init__.py``` 
Edit ```database_setup.py, __init__.py```
Change ```engine=create_engine(sql....)``` to 
```
create_engine('postgresql://catalog:password@localhost/catalog')
```

Install pip ``` sudo apt-get install python-pip```  
User pip to install requirements.txt
```
sudo pip install -r requirements.txt
```
Install psycopg2 ```sudo apt-get -qqy install postgresql python-psycopg2```  
Create database schema ```sudo python database_setup.py```  

### Configure Host
Create a FlaskApp.conf file in 
```
sudo vim /etc/apache2/sites-available/FlaskApp.conf
```
Add the following: 
```
<VirtualHost *:80>
	ServerName <your public IP>
	ServerAdmin <you email>
	WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
	<Directory /var/www/FlaskApp/FlaskApp/>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www/FlaskApp/FlaskApp/static
	<Directory /var/www/FlaskApp/FlaskApp/static/>
		Order allow,deny
		Allow from all
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

<strong>Create a .wsgi file under /var/www/FlaskApp</strong>  
```
cd /var/www/FlaskApp
sudo vim /flaskapp.wsgi
```
add the following: 
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'
```

Enable the virutual host: ```sudo a2ensite FlaskApp```  
May have to restart apache2 server: ```sudo server apache2 restart```  

You sould be able to navigate to the PUblic IP of the lighsail server and app should be running.
you will have to add in <ip>/genres
```
34.200.229.181/genres/
```

## Resources
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
https://mudspringhiker.github.io/deploying-a-flask-web-app-on-lightsail-aws.html


