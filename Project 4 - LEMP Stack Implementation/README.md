# Deploying a LEMP Stack Web Application on AWS Cloud
***
A LEMP Stack is a solution stack that is being used in deploying web applications. It stands for Linux, Nginx, MySql and php, perl or python. 

**Linux**: this is an operating system which serves as the backbone of the LAMP Stack, and it is actively used in deploying other components.

**Nginx**: is a web server software which via HTTP requests processes requests and transmits information via the internet.

**MySQL**: use for creating and maintaining dynamic databases. It supports SQL and relational tables and provides a DBMS(Database Management System).

**PHP, PERL or PYTHON**: this represents programming languages which effectively combines all the elements of the LAMP stack and is used to make web applications execute, but we will be making use of 'PHP'.

## To Set-up Linux

1. First setup an AWS EC2 Ubuntu Instance. Use a .pem key when creating the Instance and save securely 
![Alt text](<Images/Screenshot (169).png>)

2. Then I connected into the instance using git bash `ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`, and updated the linux package.
![Alt text](<Images/Screenshot (152).png>)


## To Set-up Nginx

1. To Install Nginx I used the command `sudo apt install nginx`
![Alt text](<Images/Screenshot (153).png>)

2. Then I checked it was running with the `sudo systemctl status nginx`
![Alt text](<Images/Screenshot (154).png>)

3. Then I created a rule on the Instance to allow connection through port 80.
![Alt text](<Images/Screenshot (150).png>)

4. Then I used `curl http://localhost:80` to check that we could connect

5. Then I checked that I Nginx could receive requests over the internet using `http://<Public-IP-Address>:80`, Public IP can be gotten on the instance on AWS console or by using the command `curl -s http://169.254.169.254/latest/meta-data/public-ipv4`
![Alt text](<Images/Screenshot (162).png>)
![Alt text](<Images/Screenshot (155).png>)


## To Set-up MySQL

1. I used `sudo apt install mysql-server` to install MySQL.
![Alt text](<Images/Screenshot (162-2).png>)

2. Then I used `sudo mysql` to login to the MySQL console. After which I used `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';` to give the root user a password.
Then I exited the MySQL console and used `sudo mysql_secure_installation`. I put in the password and answered yes for all questions and setting a password validation of 1.
![Alt text](<Images/Screenshot (163).png>)

3. Then I logged back in to the console with `sudo mysql -p` to test, and typed in the password. Then I exited the console.
![Alt text](<Images/Screenshot (164).png>)


## To Set-up PHP

1. I used `sudo apt install php-fpm php-mysql` to install PHP.
![Alt text](<Images/Screenshot (164-2).png>)


## Configuring Nginx to PHP Processor

1. First I created the folder ProjectLamp with `sudo mkdir /var/www/projectLEMP`. Then I assigned ownership to my current user account uing `sudo chown -R $USER:$USER /var/www/projectLEMP`.

2. Then I created a blank file in sites-available with `sudo nano /etc/nginx/sites-available/projectLEMP`.
I then pasted the details below in:
```
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
``` 

3. Then I activated the configuration by linking to the config file from Nginx `site-enabled` directory using `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`.

4. I used `sudo nginx -t` to test for errors.

5. Next I disabled the default Nginx host using `sudo unlink /etc/nginx/sites-enabled/default`.

6. I then reloaded Nginx with `sudo systemctl reload nginx`.

7. The new website is now active, but the folder is empty. I used `sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html` to create a index.html file with an echo containing a message. Go to the website `http://<Public-IP-Address>:80` to confirm.
![Alt text](<Images/Screenshot (156).png>)
![Alt text](<Images/Screenshot (157).png>)
 
![Alt text](<Images/Screenshot (166).png>)

## Testing PHP with Nginx

1. First I created an  info.php file using `nano /var/www/projectLEMP/info.php`. then I copied the following into it:
```
<?php
phpinfo();
```
2. To access the page use `http://<server_domain_or_IP>/info.php`.
![Alt text](<Images/Screenshot (158).png>)
![Alt text](<Images/Screenshot (159).png>)

3. I then used `sudo rm /var/www/your_domain/info.php` to delete the info.php file
![Alt text](<Images/Screenshot (167-2).png>)

## Retreive data from MySQL

1. Login to the console using `sudo mysql -p`

2. Create an example database `CREATE DATABASE 'example_database';`

3. Create a new user with a pass word. `CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

4. Then give the user access to the database. `GRANT ALL ON example_database.* TO 'example_user'@'%';` - This gives him all priviledges on example database.
Then exit.

5. We can test if the user has the permissions by logging in as the user. Use `mysql -u example_user -p` and type in the password.

6. To confirm that you have access to example database, type in `SHOW DATABASES;`.

7. Next, create a test table using `CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));`

8. Insert a row into the test table. `INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

9. Query the table. `SELECT * FROM example_database.todo_list;`. Then exit.

10. Next, we create and open a PHP file in projectLEMP directory. Using `nano /var/www/projectLEMP/todo_list.php`. Then paste the following in it: 
```
<?php
$user = "example_user";
$password = "PassWord.1";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```

11. Next, use `http://<Public_domain_or_IP>/todo_list.php` access the page.
![Alt text](<Images/Screenshot (167-3).png>)
![Alt text](<Images/Screenshot (168).png>)
![Alt text](<Images/Screenshot (160).png>)
![Alt text](<Images/Screenshot (161).png>)









