# Linux Server Configuration for deploying python Flask Application



###  **Introduction**:

This readme file is intended to provide a summary of packages installed in an AWS lightsail Linux server, it will provide all the steps that were taken to configure ubuntu Linux server for deploying a python flask application.

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
        
3. Create a new user account named grader
    `adduser grader `
     set a strong password, the password is set to "grader"
  
4. Provide the grader user a sudo access
   use the command `sudo adduser <username> sudo`  
  
5. Enforce keypair authentication 
    
6. Generated keypair in local machine
    
7. Installed public key in grader user

8. Installed Apache 2.0
9. Installed postgresql
10. Created a database user named catalog with password as linuxconfig 
11. Created a database named itemcatalog with owner as catalog

 ### **How to access the  server**
 
         server ip address: 34-204-108-139
         server URL: http://ec2-34-204-108-139.compute-1.amazonaws.com/
         ssh port: 2200
       

  How to access the server as a grader user:
        
       ssh into server as a grader user using the syntax below
       ssh grader@34.204.108.139 port private key 
       where port equals 2200
       private key is LinuxUdacityCourse`
       
       
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

 #### paraphrase for the key is :  **grader**
       