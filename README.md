# Linux-Server-Configuration

### Project Discription
Initialisation of Linux distribution on a virtual machine and make it to host applications on web, the process include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers

##### Live At: http://ec2-34-224-40-187.compute-1.amazonaws.com/
  * IP Address: 34.224.40.187
  * SSH Port: 2200
  
### Follow these instructions to Configure Linux server

  * First Create Account on Amazon Web Server :- https://aws.amazon.com
  * Then login to amazon web server consol
  * Then in services section choose Lightsail
  * Now Create a instance by choosing instance image as os only the choose ubuntu
  * Then bootup your virtual machine
  
#### 1. Create New User
  * sudo adduser grader
  
#### 2. Granting grader permission
  * sudo visudo
  * Inside that add grader ALL=(ALL:ALL) ALL
  * save file
  * Add grader to/etc/suoders.d/
  * In it write grader ALL=(ALL:ALL) ALL
  * Add root to /etc/sudoers.d/ and again write the above command.
    
#### 3. Now Update and Upgrade Your virtual machine
 * sudo apt-get update
 * sudo apt-get upgrade
 * Change the SSH port from 22 to 2200 and other SSH configuration required from grading rubic
 * nano /etc/ssh/sshd_config add port 2200 below port 22
 * while in the file also change Permit Root Login prohibit-password to Permit Root Login no to disallow root login
 * Change *Password Authentication* from no to yes. We will change back after finishing SHH login setup
 * save file by:- nano: ctrl+x, Y, Enter
 * Restart ssh service  :- sudo service ssh reload
 
#### 4. Genrating SSH keys and copy to server manually:
 * Generate SSH key pair with typing following command in ternimal: *ssh-keygen*
 * save keygen file in ssh directory in : /home/ubuntu/.ssh/item-catalog
 * You can add a password to secure login
 * Change SSH port number configuration in Amazon lightsail in networking option to 2200.
#### 5. Login Into Grader User Account:
 * ssh -v grader@34.224.40.187 -p 2200
 * create .ssh directory :- mkdir .ssh
 * create file to store key :- touch .ssh/authorized_keys
 * On local machine read contents of the public key :- cat .ssh/item-catalog.pub
 * Copy the key from file and paste in the file just created in grader :- nano .ssh/authorized_keys
 * save file :- nano: ctrl+x, Y, Enter
 * Set permissions for files:sudo chmod 700 .ssh chmod 644 .ssh/authorized_keys
 * Change Password Authentication from yes back to no :- nano /etc/ssh/sshd_config
 * save file :- nano: ctrl+x, Y, Enter
 * login with key pair: ssh grader@34.224.40.187 -p 2200 -i ~/.ssh/item-catalog
#### 5. Configuring the Uncomplicated Firewall (UFW)
 * Allow incoming connections for SSH (port 2200), HTTP (port 80), NTP (port 123)
 * To check UFW status is inactive :- sudo ufw status 
 * Deny all incoming by default :- sudo ufw default deny incoming
 * Allow outgoing by default :- sudo ufw default allow outgoing
 * Allow SSH :- sudo ufw allow ssh
 * Allow SSH on port 2200 :- sudo ufw allow 2200/tcp
 * Allow HTTP on port 80 :- sudo ufw allow 80/tcp
 * Allow NTP on port 123 :- sudo ufw allow 123/udp
 * Turn on the firewall :- sudo ufw enable
#### 6. Configuring local timezone to UTC
 * From prompt After running this command select none of the above. Then select UTC :-sudo dpkg-reconfigure tzdata
#### 7. Installing and configure Apache to serve a Python mod_wsgi Application
 * Check if it works at public IP address given during setup :- sudo apt-get install apache2 
 * Install mod_wsgi :- sudo apt-get install libapache2-mod-wsgi
 * Configure Apache to handle requests using the WSGI module sudo nano /etc/apache2/sites-enabled/000-default.conf
 * Add :- WSGIScriptAlias / /var/www/html/myapp.wsgi before '</VirtualHost>' closing line
 * Save file :- nano: ctrl+x, Y, Enter
 * Restart Apache :- sudo apache2ctl restart
#### 8. Clone Your Repository from which project is to download and server on web

#### 9. Installing Git
 * sudo apt-get install git
 * Install python dev
 * verify WSGI is enabled if not enable it
 * Install python-dev package sudo apt-get install python-dev
 * Verify wsgi is enabled sudo a2enmod wsgi
#### 10. Create Flask App
 * cd /var/www
 * sudo mkdir catalog
 *  cd catalog
 * sudo mkdir catalog
 * cd catalog
 * sudo mkdir static templates
 * sudo nano init.py from flask import Flask app = Flask(__name__) @app.route("/") def hello(): return "Hello (This is The      Project!)" if __name__ == "__main__": app.run()
 * flask:
   * sudo apt-get install python-pip
   * sudo pip install virtualenv
   * sudo virtualenv venv
   * sudo chmod -R 777 venv
   * source venv/bin/activate
   * pip install Flask
   * python init.py
   * deactivate
   
#### 11. Configure And Enable New Virtual Host

 * Create host config file sudo nano /etc/apache2/sites-available/catalog.conf
 * Paste the following code: 
 ```
    <VirtualHost *:80>
      ServerName 184.72.84.186/
      ServerAdmin admin@184.72.84.186/
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
 * Save file :- (nano: ctrl+x, Y, Enter)
 * Enable :- sudo a2ensite catalog
 * Create the wsgi file
 * cd /var/www/catalog
 * sudo nano catalog.wsgi
 * #!/usr/bin/python
 * import sys
 * import logging
 * logging.basicConfig(stream=sys.stderr)
 * sys.path.insert(0,"/var/www/catalog/")
 * from catalog import app as application
 * application.secret_key = 'Add your secret key'
 * install flask
 * sudo apt-get install python-pip
 * sudo pip install virtualenv
 * sudo virtualenv venv
 * sudo chmod -R 777 venv*
 * source venv/bin/activate
 * pip install Flask
 * python __init__.py
 * deactivate
 * Configure And Enable New Virtual Host
 * Create host config file sudo nano /etc/apache2/sites-available/catalog.conf
 * paste the following:
  ```
<VirtualHost *:80>
  ServerName 184.72.84.186/
  ServerAdmin admin@184.72.84.186/
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
* save file(nano: `ctrl+x`, `Y`, Enter)
* Enable `sudo a2ensite catalog`
* Create the wsgi file
* `cd /var/www/catalog`
* `sudo nano catalog.wsgi`

  ```
  #!/usr/bin/python
  import sys
  import logging
  logging.basicConfig(stream=sys.stderr)
  sys.path.insert(0,"/var/www/catalog/")
  ```
 * from catalog import app as application
 * application.secret_key = 'Add your secret key'
 * save file :- nano: ctrl+x, Y, Enter
 * sudo service apache2 restart
 * Clone Github Repo :- sudo git clone https://github.com/deep6750/item-catalog
 * make sure you get hidden files iin move shopt -s dotglob. Move files from clone directory to catalog mv               /var/www/catalog/item-catalog/* /var/www/catalog/catalog/
 * remove clone directory sudo rm -r devpost
 * make .git inaccessible
 * from cd /var/www/catalog/ create .htaccess file sudo nano .htaccess
 * paste in RedirectMatch 404 /\.git
 * save file :- nano: ctrl+x, Y, Enter
 # Install dependencies:
   * source venv/bin/activate
   * pip install httplib2
   * pip install requests
   * sudo pip install --upgrade oauth2client
   * sudo pip install sqlalchemy
   * pip install Flask-SQLAlchemy
   * sudo pip install python-psycopg2
   # Install and configure PostgreSQL:
   * Install postgres(database) :- sudo apt-get install postgresql
   * Install additional models :- sudo apt-get install postgresql-contrib
   * config database_setup.py :- sudo nano database_setup.py
   * to create database engine :- engine = create_engine('postgresql://catalog:db-password@localhost/catalog')
   * repeat for project.py
   * copy main project.py file into the init.py file mv project.py __init__.py
   * Add catalog user :- sudo adduser catalog
   * login as postgres super user :- sudo su - postgres
   * enter postgrespsql
   * Create user catalog CREATE USER catalog WITH PASSWORD 'db-password';
   * Change role of user catalog to create DBALTER USER catalog CREATEDB;
    # * List all users and roles to verify :- \du
      * Create new DB "catalog" with own of catalogCREATE DATABASE catalog WITH OWNER catalog;
      * Connect to database\c catalog
      * Revoke all rights :- REVOKE ALL ON SCHEMA public FROM public;
      * Give accessto only catalog role :- GRANT ALL ON SCHEMA public TO catalog;
      * Quit postgres :- \q
      * logout from postgres super user :- exit
      * Setup your database schema :- python database_setup.py
#### 12 fix OAuth from google developer account to work with hosted Application
 * Google wont allow the IP address to make redirects so we need to set up the host name address to be usable.
   go to http://www.hcidata.info/host2ip.cgi to get your host name by entering your public IP address Udacity gave you.
   * open apache configbfile :- sudo nano /etc/apache2/sites-available/catalog.conf
   * below the ServerAdmin paste ServerAlias YOURHOSTNAME
   * make sure the virtual host is enabled :- sudo a2ensite catalog
   * restart apache server :- sudo service apache2 restart
 * In google developer console add host name and IP address to Authorized Javascript origins. And add                HOSTNAME/ouath2callback to the Authorized redirect URIs.
