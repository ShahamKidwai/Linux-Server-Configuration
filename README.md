# Linux Server Configuration for hosting a web application



###  **Introduction**:

This readme file is intended to provide a summary of packages installed in an AWS lightsail Linux server, it will provide all the steps that were taken to configure ubuntu Linux server for deploying a python flask web application.

   ###  **Packages Installed**:

 1. Apache 2.0
 2. Postgresql
 3. Python WSGI
 4. Python Pip
 5. Other needed python packages
 6. Virtualenv
 7. Git
 8. oauth2
 
###  **Setting up the Linux server configuration:**

#####  **I. Getting the Linux server:**
 
 An amazon lightsail instance was created, this provided a barebones Linux server with the needed space to run our application, once an instance had been created, then the ports 123(NTP), 2200 were added onto the amazon lightsail firewall, the next steps were to secure the server, install the needed packages and configure the server to run our application.  
 

#####  **II. Securing the Server:**
 
1. First update and upgrade the software packages 
 
        `sudo apt-get update`
 
        `sudo apt-get upgrade`


2. Change standard ssh port to 2200
   
   open the ssh config file
         sudo nano /etc/ssh/sshd_config

   change `port 22` to `port 2200`
   
   change `PermitRootLogin yes` to `PermitRootLogin no`
   
   change `PasswordAuthentication yes` to `PasswordAuthentication no`
   
   save `ctrl+o` and exit `ctrl+x`
   
   Restart ssh for the changes to take effect: 
   
   `sudo service ssh restart`


3. Configure uncomplicated firewall
    
    Disable all incoming traffic and allow all outgoing traffic:

        `sudo ufw default allow outgoing`

        `sudo ufw default deny incoming`

     Allow connections for SSH, HTTP, and NTP:

        `sudo ufw allow 2200`

        `sudo ufw allow 123`

        `sudo ufw allow 80`

     Turn on the firewall:

        `sudo ufw enable`
        
4. Create a new user account named grader
    `adduser grader `
     set a strong password, the password is set to "grader"
  
5. Provide the grader user a sudo access
   using the command `sudo adduser <username> sudo` 

6. Generate a keypair on a  local machine using the 
   command `ssh-keygen` 

7. Enter a paraphrase: grader
    
8. Install public key on a server

9. Create a directory .ssh using the command `mkdir .ssh`

10. Create a new file authorized_keys within this directory using the command
`touch .ssh/authorized_keys`

11. Go ahead and copy the public key stored in local machine into this file, first open the editor and then paste the contents of the public key:

      `nano .ssh/authorized_keys` and then paste the public key onto this file, and save this file using the command `ctrl+x`
      
12. Go ahead set the correct permission on .ssh directory and the file authorized_keys

        ` chmod 700 .ssh`
   
        `chmod 644 .ssh/authorized_keys`

13. Go ahead and restart ssh using the command `sudo service ssh restart`

14. Logout and reconnect as a grader but this time using the private key
   
      ssh grader@54-87-221-214 -p 2200 -i ~/.ssh/LinuxUdacityCourse

      
      where **LinuxUdacityCourse** is a name of the key



15. Once connected as grader user, change the timezone to UTC, 

16. Install postgresql using the command `sudo apt-get install postgresql`
17. Create a database user named catalog with password as linuxconfig 
18. Create a database named itemcatalog with owner as catalog

19. install git, using the command `sudo apt-get insall git`


20. Install Apache 2.0 sudo apt-get install apache2

21. Configure Apache to hand off request to WSGI module
    first step is to install  and enable mod_wsgi: 
    sudo apt-get install libapache2-mod-wsgi python
    and endable it using the command sudo a2enmod wsgi

22. Move  /var/www directory

23. create a directory FlaskApp and clone the github  repository in this directory using the command git clone path_to_repository.
  
24. After cloning move to directory /var/www/FlaskApp/ItemCatalog
25. Rename the file project.py to __init__.py
26. Save and close the file
27. Install python, using the command sudo apt-get install python-pip

28. Open the database setup file and make the following changes:

    cd /var/www/FlaskApp/tutor_site

    sudo nano db_setup.py

    change

    engine = create_engine('sqlite:///itemcatalog.db')

    to

    engine = create_engine("postgresql://catalog:linuxconfig@localhost/itemcatalog")
    save ctrl+O and exit ctrl+X

   Make a similar change in your __init__.py file:

   `nano __init__.py`

   change

   engine = create_engine('sqlite:///itemcatalog.db')

   to

  engine = create_engine("postgresql://catalog:linuxconfig@localhost/itemcatalog")
   save ctrl+O and exit ctrl+X


29. Install virtual environment using the command `sudo pip install virtualenv` this is the environment where our application will run.


29. give the name to this temporary environment using the command `sudo virtualenv venv`

30. Activate this environment using the command 
    source venv/bin/activate

31. `sudo pip install Flask`

32. Install all the additional modules
    
    `sudo pip install httplib2`

    `sudo pip install requests`

    `sudo pip install oauth2client`

33. Create your database: `sudo python db_setup.py`

34. open __init__.py and change the path to client_secret.json to absolute path. 

     change

         CLIENT_ID = json.loads(open('client_secrets.json', 'r').read())['web']['client_id']

       to 

         CLIENT_ID = json.loads(open('/var/www/FlaskApp/ItemCatalog/client_secrets.json', 'r').read())['web']['client_id']
    
    change

        oauth_flow = flow_from_clientsecrets('client_secrets.json', scope='')

      to

        oauth_flow = flow_from_clientsecrets('/var/www/FlaskApp/ItemCatalog/client_secrets.json', scope='')


35.   Run   __init__.py  file using the command  `sudo python __init__.py`

  If everything seems good then it should display 
  
  “Running on http://localhost:5000/” or "Running on http://127.0.0.1:5000/"

go ahead and deactivate the environment using the command `deactivate`

sudo nano /etc/apache2/sites-available/FlaskApp.conf
Add the following lines of code to the file to configure the virtual host. Be sure to change the ServerName to your domain or cloud server's IP address:

<VirtualHost *:80>
		ServerName mywebsite.com
		ServerAdmin admin@mywebsite.com
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


Save and close the file.

Enable the virtual host with the following command:
sudo `a2ensite FlaskApp`

and disable the host `a2dissite 000-default`

Apache uses the .wsgi file to serve the Flask app. 

Move to the /`var/www/FlaskApp` directory and create a file named `flaskapp.wsgi` with following commands:

`cd /var/www/FlaskApp`

`sudo nano flaskapp.wsgi `

Add the following lines of code to the flaskapp.wsgi file:

#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'

Go ahead and restart apache 

`sudo service Apache 2 restart`

 ### **How to access the  server**
 
         server ip address: 54-87-221-214
         server URL: http://ec2-54-87-221-214.compute-1.amazonaws.com/
         ssh port: 2200
       

  How to access the server as a grader user:
        
       ssh into server as a grader user using the syntax below
       ssh grader@54-87-221-214 port private key 
       where port equals -p 2200
       private key is LinuxUdacityCourse`
       paraphrase for the key is : grader

       
  below are the content of the private key named LinuxUdacityCourse:

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED

DEK-Info: AES-128-CBC,1BA8DFDC142646B56DCF7C6BD292A105

OJgMdtjC9K+pZzYklhUX/9scswtuZAqbcFJTS1xIbeb3cNREZNGZFqoJRNWPkrKW
LluUEBubMBTmK4WB8LiThdJiFVI53zdIe1FyOnT2mxGQy6gTAmShYDvH1rGHngWb
bxQVtz0gFiExyzOGZ8jbNWu8S49gm8BY0erkNCrPWHq3Ghpwx8Olq8d5UCMd3Sat
j+3mQ8wQRD0fy1JsRCUcy/ToICHYVgL7opiMVvwGRyXjtSm5lr3jHM4O7Q39K+xN
OJoxRqUDKE1MMiR//853zUltxa80qbMmMmTRokNfaWU/pgSR0OlkNUQ5zYbcSwbE
b2YjZYLUW7e77UJQgHLxLLZt6lUQfhP3VxxFbtAZUeVzjWkYmCOTLZX1ZR4Xc31L
JwNM4LIlQt5Ks3GEwbJ/mOxUqxKnSjAWwz5INiDq0/7kK2iaizNkWRg50k2F3Svs
mjS6ejrTq6DcW1Z15WVQ4q4lM4PaiWMmziqX/YvKBvBM6bRaoaCrOY1NWU7NnEsc
hkkXEz5DIjddfcyiEeonuQ5kUJEe0IZcPu3/evqAKiDr5F82LBtgpAy4K8VQaQzC
tSDZIOZX22ZwQhLq15OtkEPBrJpyYGffl78ppLTzQpYi11wxMVtD9DCsUUVHcj0P
Oss0qnQp9nAxzfK4HNYZ0uQ5QDY4oJX8ioVujJoIQfZFcQhRLYKmIARynL6mMXly
zgBmDVejSiqy33PMb8o7aMzCRvaGLcu+K4hvaz+MkxcDGJ/u7/5uDe+5wrdn7WX2
A+qacOrXUXPxByq4U5oORX3W47iDFQTI3A54nmXralKM0NewXkhZic/ANByBQWt3
zhD5AYgF725+kLz0TpZjrDLvGMZHIvdO/9eiC2nJU1dyABYz7sfIKhwq2EdsNstc
L8ab2vvk4mgzrhOGv8lwYU4za68vWwO8+3apWoV5Je5TTTfP2dTMZCGeU4hcKPx9
zirEDp/E5CbVmIccUD3x76qTmyHrus8xmuiWZ9sL6rmkaHmMfO4OilqwT8g6NAFU
fCfZHW9wwJJvpNSmsa0P596tdl53moWMcA9OvlcCVyBZM+JdOzwEJoj+9jGP4oF0
iU5V0XxMro7J74uo9E8TJzYg+J2QO7BLx1b6wB0ot4DnUpvfZqinXV8el31voWu0
6vk/MpLxBy8KyfXioOR8pyB25/lZ8dsqhfF4qRvKrccbgfzgBxuPdxMcxxSHgJYm
mUFHADwxLi8Ig7yQhmRKbRL2oTMO9FgIyBfbQ9LIW1ToT67tcA5+CGNBY/bYICR9
DIi3rEV6653GsbVciT2IouF6tZ2hx+4KQsvmImG4RpGFRYdgMMuweY+Nw/1lWjq3
O0+v1xdNpzMLDvRwGYrN9vaUOcpi03JN+oRCr8F+7JH9dBkyGuEhW00FzpLDtwJm
RyFQb1EtjAHGhSmvxgdzgFyfbcvvbQgzwf6VKs6QxuIt/4c7yuweweyaJjlKrh9l
ktiO19ujMebAruuYAGLA3bzuaKfdJjK0uy/QyK35tL/I4RW4FeFosxaAnXcSYR3i
ckBY81A7VRy8OsBzOGxMb3rLBsfhtqPCdGdbX/TUDFI4e4EZzjwrIYXJJbYfg4xk

-----END RSA PRIVATE KEY-----

 
