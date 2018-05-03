## Project 5: Linux Server Configuration

### Description of The Project

>You will take a baseline installation of a Linux server and prepare it to host your web applications. You will secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.

>A deep understanding of exactly what your web applications are doing, how they are hosted, and the interactions between multiple systems are what define you as a Full Stack Web Developer. In this project, you’ll be responsible for turning a brand-new, bare bones, Linux server into the secure and efficient web application host your applications need.

## Server Access Information

Public IP Address: 18.222.36.56

SSH Port: 2200

SSH Login to Server: ssh -i ~/.ssh/udacity_key.rsa grader@18.222.36.56 -p 2200

Passphrase: password

Web Application URL: [http://ec2-18-222-36-56.us-east-2.compute.amazonaws.com](http://ec2-18-222-36-56.us-east-2.compute.amazonaws.com)

### Get Your Server:

1.  Start a new Ubuntu Linux server instance on [Amazon Lightsail](https://lightsail.aws.amazon.com/).
2.  Download private keys and write down your public IP address.
3.  Move the private key file into the folder ~/.ssh:  
`$ mv ~/Downloads/udacity_key.rsa ~/.ssh/`
4.  Set file rights (only owner can write and read.):  
`$ chmod 600 ~/.ssh/udacity_key.rsa`
5.  SSH into the instance:  
`$ ssh -i ~/.ssh/udacity_key.rsa ubuntu@PUPLIC-IP-ADDRESS`
  
### Create New User And Give User The Permission To Sudo

1.  Create new user:
`$ sudo adduser grader`
2.  Open sudo configuration:
`$ visudo`
3.  Add the following line below **root ALL...**:
`grader ALL=(ALL:ALL) ALL`

Source: [DigitalOcean][1]

### Update And Upgrade All Currently Installed Packages

1.  Update the list of available packages and their versions:
`$ sudo apt-get update`
2.  Install newer vesions of packages you have:
`$ sudo sudo apt-get upgrade`

Source: [Ask Ubuntu][2]

### Change The SSH Port From 22 To 2200 And Configure SSH Access

1.  Open the ssh config file:
`$ sudo nano /etc/ssh/sshd_config`
2.  Comment out Port 22 and add Port 2200.
3.  Change **PermitRootLogin** from **without-password** to **no**.
4.  Temporalily change **PasswordAuthentication** from **no** to **yes**.
5.  Add line **UseDNS no**.
6.  Add line **AllowUsers grader**.
7.  Restart SSH Service:
`$ service sshd restart`

Source: [Ask Ubuntu][3]

### Configure Key-Based Authentication For User Grader

1.  Generate a SSH key pair on your local machine:
`$ ssh-keygen`
2.  Create the authorized keys file on the VM:
`$ touch /home/grader/.ssh/authorized_keys`
3.  Copy the contents of the **udacity_key.rsa.pub** file from your local machine to your **/home/grader/.ssh/authorized_keys** file on your VM.
`$ cat .ssh/udacity_key.rsa.pub` (on local machine to copy)
4.  Setup file permissions:
`$ sudo chmod 700 /home/grader/.ssh`
`$ sudo chmod 644 /home/grader/.ssh/authorized_keys`
`$ sudo chown -R grader:grader /home/grader/.ssh` (to change owner from **ubuntu** to **grader**)
5.  Now log into the remote VM through SSH on port 2200 with the following command:
`ssh -i ~/.ssh/udacity_key.rsa grader@18.222.36.56 -p 2200`
6.  Open SSHD configuration file:
`$ sudo nano /etc/ssh/sshd_config`
7.  Change **PasswordAuthentication** back from **yes** to **no**.

Source: [DigitalOcean][4]

### Resolve Warning Message *"sudo: unable to resolve host ..."*

1.  Open hostname file on local machine:
`$ nano /etc/hostname`
2.  Copy the hostname.
3.  Add the hostname to the hosts file on VM:
`$ sudo nano /etc/hosts`

Source: [Ask Ubuntu][5]

### Configure The Uncomplicated Firewall (UFW) To Only Allow Incoming Connections For SSH (port 2200), HTTP (port 80), And NTP (port 123)

1.  Turn UFW on with the default set of rules:
`$ sudo ufw enable` 
2.  Check the status of UFW:  
`$ sudo ufw status verbose`
3.  Allow incoming TCP packets on port 2200 (SSH):  
`$ sudo ufw allow 2200/tcp` 
4.  Allow incoming TCP packets on port 80 (HTTP):  
`$ sudo ufw allow 80/tcp` 
5.  Allow incoming UDP packets on port 123 (NTP):  
`$ sudo ufw allow 123/udp`

Source: [Ubuntu documentation][6]

### Configure The Local Timezone To UTC

1.  Open the timezone selection dialog:  
`$ sudo dpkg-reconfigure tzdata`
2.  Then chose **None of the above**, then **UTC**.
3.  Setup the ntp daemon ntpd for regular and improving time sync:  
`$ sudo apt-get install ntp`

Source: [Ubuntu documentation][7]

### Install And Configure Apache To Serve A Python mod_wsgi Application

1.  Install Apache web server:  
`$ sudo apt-get install apache2`
2.  Open a browser and open your public ip address, e.g. http://18.222.36.56/ - It should  say **It works!** on the top of the page.
3.  Install **mod_wsgi** for serving Python apps from Apache and the helper package **python-setuptools**:  
`$ sudo apt-get install python-setuptools libapache2-mod-wsgi`
4. Restart the Apache server for mod_wsgi to load:  
`$ sudo service apache2 restart`

Source: [Udacity][8]

### Install And Configure GIT

1.  Install Git:  
`$ sudo apt-get install git`
2.  Set your name, e.g. for the commits:  
`$ git config --global user.name "YOUR NAME"`
3.  Set up your email address to connect your commits to your account:  
`$ git config --global user.email "YOUR EMAIL ADDRESS"`

Source: [GitHub][9]

### Setup For Deploying A Flask Application On Ubuntu VPS

1.  Extend Python with additional packages that enable Apache to serve Flask applications:  
`$ sudo apt-get install libapache2-mod-wsgi python-dev`
2.  Enable mod_wsgi (if not already enabled):  
`$ sudo a2enmod wsgi`
3.  Move to the www directory:  
`$ cd /var/www`
4.  Setup a directory for the app, e.g. catalog:  
```
$ sudo mkdir catalog
$ cd catalog
$ sudo mkdir catalog  
$ cd catalog
$ sudo mkdir static templates
```
5.  Create the file that will contain the flask application logic:  
`$ sudo nano __init__.py`
6.  Paste in the following code:
```
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Hello, I love Digital Ocean!"
if __name__ == "__main__":
    app.run()
```

Source: [DigitalOcean][10]

### Installing Flask

1.  Install pip installer:  
`$ sudo apt-get install python-pip` 
2.  Install virtualenv:  
`$ sudo pip install virtualenv`
3.  Set virtual environment to name 'venv':  
`$ sudo virtualenv venv`
4.  Enable all permissions for the new virtual environment:   
`$ sudo chmod -R 777 venv`
5.  Activate the virtual environment:  
`$ source venv/bin/activate`
6.  Install Flask inside the virtual environment:  
`$ pip install Flask`
7.  Run the app:  
`$ python __init__.py`
8.  Deactivate the environment:  
`$ deactivate`

Source: [DigitalOcean][10]

### Configure And Enable A New Virtual Host

1.  Create a virtual host config file:  
`$ sudo nano /etc/apache2/sites-available/catalog.conf`
2.  Paste in the following lines of code and change names and addresses for  your application:
```
<VirtualHost *:80>
    ServerName 18.222.36.56
    ServerAdmin admin@18.222.36.56
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
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
</VirtualHost>
```
3.  Enable the virtual host:  
`$ sudo a2ensite catalog`

Source: [DigitalOcean][10]

### Create The *.wsgi* File And Restart Apache

1.  Create wsgi file:  
```
$ cd /var/www/catalog
$ sudo nano catalog.wsgi
```
2.  Paste in the following lines of code:  
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")
from catalog import app as application
application.secret_key = 'Add your secret key'
```
3.  Restart Apache:  
`$ sudo service apache2 restart`

Source: [DigitalOcean][10]

### Clone GitHub Repository And Make It Web Inaccessible

1.  Clone project 3 solution repository on GitHub:  
`$ git clone https://github.com/crensma11/sport_gear_app.git`
2.  Remove the prviously created **__init__.py** file from the **/var/www/catalog/catalog/** directory. 
3.  Move all contents of created **sport_gear_app** directory to **/var/www/catalog/catalog/** directory and delete the leftover empty directory.
4.  Create and open .htaccess file:  
```
$ cd /var/www/catalog/
$ sudo nano .htaccess
```
5.  Paste in the following:  
`RedirectMatch 404 /\.git`

Source: [Stackoverflow][11]

### Install Needed Modules And Packages

1.  Activate virtual environment:  
`$ source venv/bin/activate`
2.  Install httplib2 module in venv:  
`$ pip install httplib2`
3.  Install requests module in venv:  
`$ pip install requests`
4.  Install oauth2client.client:  
`$ sudo pip install --upgrade oauth2client`
5.  Install SQLAlchemy:  
`$ sudo pip install sqlalchemy`
6.  Install the Python PostgreSQL adapter psycopg:  
`$ sudo apt-get install python-psycopg2`

### Install And Configure PostgreSQL

1.  Install PostgreSQL:  
`$ sudo apt-get install postgresql postgresql-contrib`
2.  Check that no remote connections are allowed (default):  
`$ sudo nano /etc/postgresql/9.5/main/pg_hba.conf`
3.  Open the database setup file:  
`$ sudo nano database_setup.py`
4.  Change the line starting with **engine** to fill in a password:  
```
  engine =create_engine('postgresql://catalog:password@localhost/catalog')
```  
5.  Change the same line in application.py and lotsofgear.py respectively.
6.  Rename application.py:  
`$ mv application.py __init__.py`
7.  Change to default user postgres:  
`$ sudo su - postgres`
8.  Connect to the system:  
`$ psql`
9.  Create user catalog and set a password:  
`# CREATE USER catalog WITH PASSWORD 'password';`
10. Allow the user to create database tables:  
`# ALTER USER catalog CREATEDB;`
11. Create database:  
`# CREATE DATABASE catalog WITH OWNER catalog;`
12. Connect to the database catalog
`# \c catalog` 
13. Revoke all rights:  
`# REVOKE ALL ON SCHEMA public FROM public;`
14. Grant only access to the catalog role:  
`# GRANT ALL ON SCHEMA public TO catalog;`
15. Exit out of PostgreSQl and the postgres user:  
```
# \q
$ exit
``` 
16. Create postgreSQL database schema:
`$ python database_setup.py`
17. Populate the postgresSQL database:
`$ pythone lotsofsports.py`

Source: [DigitalOcean][12]

### Run Application

1.  Restart Apache:  
`$ sudo service apache2 restart`
2.  Open a browser and put in your public ip address as url, e.g. 18.222.36.56.  If everything works, the application should come up

### Troubleshooting

1.  If you get and internal server error, you need to check the Apache error log.  My application did not work right off the bat and one of the biggest reasons was due to not having installed all of the dependent packages for my application.  The error log was a life saver when dealing with this.
`$ sudo tail -20 /var/log/apache2/error.log`
2.  If an error shows **client_secrets.json could not be found or does not exit**.  I had this problem.  You have to update the location of this file with the complete file path for it to be recognized.

Sources: [A2 Hosting][13] & [Stackoverflow][14]

### Get OAuth Logins Working

1.  Open http://www.hcidata.info/host2ip.cgi and receive the Host name for your public IP address, e.g. 18.222.36.56 is ec2-18-222-36-56.us-east-2.compute.amazonaws.com.
2.  Open the Apache configuration files for the web app:
`$ sudo nano /etc/apache2/sites-available/catalog.conf`
3.  Paste in the following line below ServerAdmin:  
`ServerAlias ec2-18-222-36-56.us-east-2.compute.amazonaws.com`
4. Enable the virtual host:  
`$ sudo a2ensite catalog`

Source: [Apache][15]

### Get Google Authorization Working

1.  Go to the project on the Developer Console: https://console.developers.google.com/project
2.  Navigate to APIs & auth > Credentials > Edit Settings.
3.  Add your host name and public IP address to your Authorized JavaScript origins and your host name + oauth2callback to Authorized redirect URIs, e.g. http://ec2-18-222-36-56.us-east-2.compute.amazonaws.com/oauth2callback.

### Get Facebook Authorization Working

Note: I did not take the app out of development mode so I can only login.

1.  Go on the Facebook Developers Site to My Apps: https://developers.facebook.com/apps/
2.  Click on your App, go to Settings and fill in your public IP-Address including prefixed http:// in the Site URL field
3.  To leave the development mode, so others can login as well, also fill in a contact email address in the respective field, **Save Changes**, click on **Status & Review**



<!--Resources Used-->

[1]: https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps "How To Add and Delete Users on an Ubuntu 14.04 VPS"
[2]: http://askubuntu.com/questions/94102/what-is-the-difference-between-apt-get-update-and-upgrade "What is the difference between apt-get update and upgrade?"
[3]: http://askubuntu.com/questions/16650/create-a-new-ssh-user-on-ubuntu-server "Create a new SSH user on Ubuntu Server"
[4]: https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server "How To Configure SSH Key-Based Authentication on a Linux Server"
[5]: http://askubuntu.com/questions/59458/error-message-when-i-run-sudo-unable-to-resolve-host-none "Error message when I run sudo: unable to resolve host (none)"
[6]: https://help.ubuntu.com/community/UFW "UFW - Uncomplicated Firewall"
[7]: https://help.ubuntu.com/community/UbuntuTime#Using_the_Command_Line_.28terminal.29 "Ubuntu Time Management"
[8]: http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html "A Step by Step Guide to Install LAMP (Linux, Apache, MySQL, Python) on Ubuntu"
[9]: https://help.github.com/articles/set-up-git/#platform-linux "Set Up Git for Linux"
[10]: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps "How To Deploy a Flask Application on an Ubuntu VPS"
[11]: http://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible "Make .git directory web inaccessible"
[12]: https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps "How To Secure PostgreSQL on an Ubuntu VPS"
[13]: https://www.a2hosting.com/kb/developer-corner/apache-web-server/viewing-apache-log-files "How to view Apache log files"
[14]: http://stackoverflow.com/questions/12201928/python-open-method-ioerror-errno-2-no-such-file-or-directory "Python: No such file or directory"
[15]: http://httpd.apache.org/docs/2.2/en/vhosts/name-based.html "Name-based Virtual Host Support"

### Created By *Adam Crenshaw*
