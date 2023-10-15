# Deploying a LAMP Stack Web Application on AWS Cloud
***
A LAMP Stack is a solution stack that is being used in deploying web applications. It stands for Linux, Apache, MySql and php, perl or python. 

**Linux**: this is an operating system which serves as the backbone of the LAMP Stack, and it is actively used in deploying other components.

**Apache**: is a web server software which via HTTP requests processes requests and transmits information via the internet.

**MySQL**: use for creating and maintaining dynamic databases. It supports SQL and relational tables and provides a DBMS(Database Management System).

**PHP, PERL or PYTHON**: this represents programming languages which effectively combines all the elements of the LAMP stack and is used to make web applications execute, but we will be making use of 'PHP'.

## To Set-up Linux

1. First setup an AWS EC2 Ubuntu Instance. Use a .pem key when creating the Instance and save securely 
![Alt text](<Images/Screenshot (149).png>)

2. Then I changed permissions on the private key using `sudo chmod 0400 <private-key-name>.pem`. 
Then I connected into the instance using git bash `ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`, and updated the linux package.
![Alt text](<Images/Screenshot (133).png>)


## To Set-up Apache

1. To Install Apache I used the command `sudo apt install apache2`
![Alt text](<Images/Screenshot (134).png>)

2. Then I checked it was running with the `sudo systemctl status apache2`
![Alt text](<Images/Screenshot (135).png>)

3. Then I created a rule on the Instance to allow connection through port 80.![Alt text](<Images/Screenshot (150).png>)

4. Then I used `curl http://localhost:80` to check that we could connect
![Alt text](<Images/Screenshot (136).png>)

5. Then I checked that I Apache could receive requests over the internet using `http://<Public-IP-Address>:80`, Public IP can be gotten on the instance on AWS console or by using the command `curl -s http://169.254.169.254/latest/meta-data/public-ipv4`
![Alt text](<Images/Screenshot (137-2).png>)
![Alt text](<Images/Screenshot (132).png>)


## To Set-up MySQL

1. I used `sudo apt install mysql-server` to install MySQL.
![Alt text](<Images/Screenshot (137).png>)

2. Then I used `sudo mysql` to login to the MySQL console. After which I used `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';` to give the root user a password.
Then I exited the MySQL console and used `sudo mysql_secure_installation`. I put in the password and answered yes for all questions and setting a password validation of 1.
![Alt text](<Images/Screenshot (138).png>)

3. Then I logged back in to the console with `sudo mysql -p` to test, and typed in the password. Then I exited the console.
![Alt text](<Images/Screenshot (139).png>)


## To Set-up PHP

1. I used `sudo apt install php libapache2-mod-php php-mysql` to install PHP.
![Alt text](<Images/Screenshot (140).png>)

2. I checked the version using `php -v`
![Alt text](<Images/Screenshot (148-2).png>)


## To Deploy the Site
1. First I created the folder ProjectLamp with `sudo mkdir /var/www/projectlamp`. Then I assigned ownership to my current user account uing `sudo chown -R $USER:$USER /var/www/projectlamp`.

2. I then created and opened a new folder `sudo vi /etc/apache2/sites-available/projectlamp.conf` and pasted the following into it:
```
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
3. Then I used `sudo a2ensite projectlamp` to enable the virtual host and used `sudo a2dissite 000-default` to disable apaches default website.


4.The new website is now active, but the folder is empty. I used `sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html` to create a index.html file with an echo containing a message. Go to the website `http://<Public-IP-Address>:80` to confirm.
![Alt text](<Images/Screenshot (146).png>)
![Alt text](<Images/Screenshot (147).png>)


## To Enable PHP

1. First I use `sudo vim /etc/apache2/mods-enabled/dir.conf` and make the changes below inside:
```
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
2. I used `sudo systemctl reload apache2` to reload apache so changes can take effect.

3. Then I created and edited a new file called idex.php with `sudo vim /var/www/projectlamp/index.php`. I pasted the following PHP code.
```
<?php
phpinfo();
```
4. Then I removed the PHP file cause it contains sensitive information about my PHP environment.
![Alt text](<Images/Screenshot (144).png>)
![Alt text](<Images/Screenshot (145).png>)
![Alt text](<Images/Screenshot (148).png>)