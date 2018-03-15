# to open my project use "http://35.199.47.225/"
# to intery to the machine useing file of: authorized_keys
   1- download the a_key file in your machine 
   2- write in terminal >> ssh -i a_key grader@35.199.47.225 -p 2200 >> password "12345"
/////////////////////////////////////////////////////////
# Linux-server-configration
** public_ip: http://35.199.47.225 **
 
** ssh_port : 2200 **

//////////////////////////////////
#create new user

`sudo adduser grader`

`nano /etc/sudoers.d/grader`
write `grader ALL=(ALL:ALL) ALL` in file 
then change the dirctory to grader by wirte `su - grader`

//////////////////////////////////////

#  to generate public key 

write `ssh-keygen` in your local machine , then entered the file name(in my case magdy_key) 
you will get 2 files (the_file_name) & (the_file_name.pub)

copy the content of (the_file_name.pub) by using `sudo nano the_file_name.pub` 
go to your vpn and write this in the terminal

  `su - grader`
  `mkdir .ssh`
  `touch .ssh/authorized_keys`
  `nano .ssh/authorized_keys`

#  past the *.pub* file in the *authorized_keys* file and exit it
#  change the file permissions by write

  `chmod 700 .ssh`
  `chmod 644 .ssh/authorized_keys`
  `service ssh restart`

#  in your machine now you can ssh connect to your instance using line ssh -i <the_file_name_in_.ssh>grader@<public_ip> or by just ssh -v grader@<public_ip> and the os will look for the file in .ssh folder
//////////////////////////////////////////
#  Change ssh default port and add UFW

  "nano sudo vim /etc/ssh/sshd_config" in this file change>> port 2200 or any availabe number
  then exit sudo service ssh restart

# enable your firewall

  `enable UFW`
  `sudo ufw default deny incoming`
  `sudo ufw default allow outcoming`
  `sudo ufw allow 80/tcp`
  `sudo ufw allow 2200/tcp`
  `sudo ufw allow 123/udp`
  `sudo ufw enable`

#  Update & Upgrade & edit timezone
  `sudo apt-get update`
  `sudo apt-get upgrade`

#  install apache2 & python wsgi
  `sudo apt-get install apache2`
  `sudo apt-get install libapache2-mod-wsgi`
  `sudo service apache2 restart`
  
#  check your public_ip in the browser ( you will get apache2 default page)
#  install postgres & create the database
  
  `sudo apt-get install postgresql`

  `sudo su - postgres` change to postgres user

  `psql` >> go to the shell

    -CREATE DATABASE data;
    -CREATE USER magdy;
    -ALTER ROLE magdy WITH PASSWORD 'password';
    -GRANT ALL PRIVILEGES ON DATABASE data TO magdy;
/////////////////////////////////////////////////////////

#  Install git, clone and setup your Catalog App project.
    1-Install Git using sudo apt-get install git
    2-Use cd /var/www to move to the /var/www directory
    3-Create the application directory sudo mkdir FlaskApp
    4-Move inside this directory using cd FlaskApp
    5-Clone the Catalog App to the virtual machine git clone https://github.com/alaashaher/item-catalog-Application
    6-Rename the project's name sudo mv ./ITEM_CATALOG_APP ./FlaskApp
    7-Move to the inner FlaskApp directory using cd FlaskApp
    8-Rename website.py to __init__.py using sudo mv main_page.py __init__.py
    9-Edit DataBase.py, main_page.py and DataBase_operation.py and change engine = create_engine('sqlite:///categories_menu.db') to engine = create_engine('postgresql://alaa:password@localhost/data')
    10-Install pip sudo apt-get install python-pip
    11-Use pip to install dependencies sudo pip install -r requirements.txt
    12-Install psycopg2 sudo apt-get -qqy install postgresql python-psycopg2
    13-Create database schema sudo python database_setup.py
//////////////////////////////////////////////////////////////////
#  create .config file and wsgi file
  "sudo nano /etc/apache2/sites-available/Website.conf"

    then write :

    <VirtualHost *:80>
        ServerName 35.199.47.225
        ServerAdmin mahmoudmagdy206@rocketmail.com
        WSGIScriptAlias / /var/www/FlaskApp/start.wsgi
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


  `sudo a2ensite Website` for enable it

#  add wsgi file in your Website folder write : "sudo nano start.wsgi" then past this :
    #!/usr/bin/python
    import sys
    import logging

    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0,"/var/www/FlaskApp/FlaskApp")

    from Website import app as application
    application.secret_key = 'Add your secret key'

# if you get error check : "sudo tail -f /var/log/apache2/error.log"

