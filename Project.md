# WEB STACK IMPLEMENTATION (LEMP STACK) IN AWS

Project Prequisite is AWS EC2 instance.

## Steps Involved
1.   INSTALLING THE NGINX WEB SERVER
2.   INSTALLING MYSQL
3.   INSTALLING PHP
4.   CONFIGURING NGINX TO USE PHP PROCESSOR
5.   TESTING PHP WITH NGINX
6.   RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

## Implementation
Since we are using the Ubuntu AMI of AWS EC2 instance to host our application. On our terminal, we will change directory to the folder we have our .pem file. In this case, it is in the Downloads folder

```
cd ~/Downloads
```

Now, we will change permissions for the private key file (.pem), otherwise we will get the error “Bad permission”

```
sudo chmod 0400 <private-key-name>.pem
```

We will then connect to the instance by running 

```
ssh -i <private-key-name>. pem ubuntu@<Public-IP-address>
```

## INSTALLING THE NGINX WEB SERVER
In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We’ll use the apt package manager to install this package.
Since this is our first time using apt for this instance session, we will start off by updating our server’s package index. Following that, we can use apt install to get Nginx installed:

```
sudo apt update
sudo apt install nginx
```
When prompted, we will enter Y to confirm that we want to install Nginx
To verify that nginx was successfully installed and is running as a service in Ubuntu, we will run:

```
sudo systemctl status nginx
```
The result should be green and running like the below since we did everything correctly.
![nginx status](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2018.39.51.png)


Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is default port that web browsers use to access web pages in the Internet.

As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80.

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).
First, let us try to check how we can access it locally in our Ubuntu shell, we will run:

```
curl http://localhost:80

```
or
```
curl http://127.0.0.1:80

```

The 2 commands above actually do pretty much the same – they use ‘curl’ command to request our Nginx on port 80 (actually you can even try to not specify any port – it will work anyway). The difference is that: in the first case we try to access our server via DNS name and in the second one – by IP address (in this case IP address 127.0.0.1 corresponds to DNS name ‘localhost’ and the process of converting a DNS name to IP address is called "resolution").

As an output we would see some strangely formatted test, we just ensured that our Nginx web service responds to ‘curl’ command with some payload.

Now, we will test how our Nginx server can respond to requests from the Internet.
In a browser of our choice, we will try to access this url. i.e, the public-ip-address of our server with :80 as a suffix.
```
http://<Public-IP-Address>:80
```
Another way to retrieve our Public IP address other than to check it in AWS Web console, is to use following command:
```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```
The URL in browser shall also work if we do not specify port number since all web browsers use port 80 by default.
For us to see the page below, it means our web server is now correctly installed and accessible through our firewall.
In fact, it is the same content that we previously got by ‘curl’ command. But this time, it is represented in nice HTML formatting by our web browser.
![nginx display](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2018.54.10.png)

## INSTALLING MYSQL
Now that we have a web server up and running, we need to install a Database Management System (DBMS) to be able to store and manage data for our site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.
 
 ```
 sudo apt install mysql-server
 ```
 
 When prompted, confirm installation by typing Y, and then ENTER.
 
 Login to the MYSQL console by typing:
 ```
 sudo mysql
 ```
 It will connect to the MySQL server as the administrative database user root
 
![SQL](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2019.15.21.png)

 Here, we will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as Omolade.9.
 ```
 ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Omolade.9'; 
```
 
 We will exit the MySQL shell with the exit command:
 
``` 
 mysql> exit 
 ```
 
Afterward, Start the interactive script by running:
``` 
sudo mysql_secure_installation
```
 
This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.
If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.
Answer Y for yes, or anything else to continue without enabling.

 If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if you enter 2 for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words e.g., PassWord.1.

 When you’re finished, test if you’re able to log in to the MySQL console by typing:
```
 sudo mysql -p
```
 
 To exit the MySQL console, type:
```
 mysql> exit
```
 Our MySQL server is now installed and secured. Next, we will install PHP, the final component in the LAMP stack.


## INSTALLING PHP
We have Nginx installed to serve your content and MySQL installed to store and manage our data. Now we can install PHP to process code and generate dynamic content for the web server.

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. We’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, we’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.
To install these 2 packages at once, we will run:
```
sudo apt install php-fpm php-mysql
```
When prompted, type Y and press ENTER to confirm installation.
You now have your PHP components installed. Next, you will configure Nginx to use them.

## CONFIGURING NGINX TO USE PHP PROCESSOR

When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use project LEMP as an example domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if we are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for our domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.
Create the root web directory for our domain as follows:
```
sudo mkdir /var/www/projectLEMP
```
Next, we will be assigning ownership of the directory with our current system user:
 
 ```
  sudo chown -R $USER:$USER /var/www/projectLEMP
```
 Then, we will create and open a new configuration file in Nginx’s sites-available directory using our preferred command-line editor. Here, we’ll be using nano:
 
 ```
sudo nano /etc/nginx/sites-available/projectLEMP
```
This will create a new blank file. Paste in the following bare-bones configuration:
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
When using nano, we can do so by typing CTRL+X and then y and ENTER to confirm.
We will activate our configuration by linking to the config file from Nginx’s sites-enabled directory:
```
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
This will tell Nginx to use the configuration next time it is reloaded. We can test our configuration for syntax errors by typing:
```
sudo nginx -t
```
We will see the following message:
![](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2019.46.54.png)

If any errors were reported, we would have had to go back to our configuration file to review its contents before continuing.
We also need to disable default Nginx host that is currently configured to listen on port 80. For this, we would run:
```
sudo unlink /etc/nginx/sites-enabled/default
```
When we are ready, we would reload Nginx to apply the changes:
```
sudo systemctl reload nginx
```
Our new website is now active, but the web root /var/www/projectLEMP is still empty. we would create an index.html file in that location so that we can test that our new server block works as expected:
```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```

Now, we would go to our browser and try to open our website URL using IP address:
```
http://<Public-IP-Address>:80
```
![](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2019.54.22.png)
	
From the result we got above, it means our Nginx site is working as expected. In the output we will see our server’s public hostname (DNS name) and public IP address. You can also access our website in our browser by public DNS name, not only by IP – try it out, the result must be the same (port is optional)
```
http://<Public-DNS-Name>:80
```
We can leave this file in place as a temporary landing page for our application until we set up an index.php file to replace it. Once we do that, remember to remove or rename the index.html file from our document root, as it would take precedence over an index.php file by default.
Our LEMP stack is now fully configured. In the next step, we’ll create a PHP script to test that Nginx is in fact able to handle .php files within our newly configured website.

## TESTING PHP WITH NGINX

We can test our LEMP stack to validate that Nginx can correctly hand .php files off to our PHP processor.
We can do this by creating a test PHP file in our document root. We will open a new file called info.php within our document root in our text editor:
```
sudo nano /var/www/projectLEMP/info.php
```
Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:
```
<?php
phpinfo();
```
We can now access this page in our web browser by visiting the domain name or public IP address we’ve set up in our Nginx configuration file, followed by "/info.php":
```
http://`server_domain_or_IP`/info.php
```
The image below is the result it displays
![](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2020.08.51.png)

The result is a web page containing detailed information about our server:
After checking the relevant information about our PHP server through that page, it’s best to remove the file we created as it contains sensitive information about our PHP environment and our Ubuntu server. We can use rm to remove the file:
```
sudo rm /var/www/projectLEMP/info.php
```

More so, we can always regenerate this file if we need it later.


## RETRIEVING DATA FROM MYSQL DATABASE WITH PHP
In this step, we will create a test database (DB) with a simple "To do list" and configure access to it, so that the Nginx website would be able to query data from the DB and display it.

Currently, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, which is the default authentication method for MySQL 8. 

We’ll need to create a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP.

We will create a database named example_database and a user named example_user, but we have a choice to replace these names with different values.

First, we will connect to the MySQL console using the root account:
```
sudo mysql -p
```
To create a new database, we would run the following command from our MySQL console:
```
mysql> CREATE DATABASE `example_database`;
```
Now we can create a new user and grant him full privileges on the database we have just created.

The following command creates a new user named example_user, using mysql_native_password as default authentication method. 
We’re defining this user’s password as password, but we should replace this value with a secure password of our own choice.
```
mysql> CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```
Now, we need to give this user permission over the example_database database:

```
mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';
```
This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on our server.

Now, we will exit the MySQL shell with:
```
mysql> exit
```

We can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:
```
mysql -u example_user -p
```
Notice the -p flag in this command, which will prompt us for the password used when creating the example_user user. After logging in to the MySQL console, confirm that we have access to the example_database database:
```
mysql> SHOW DATABASES;
```
This gave us the following output:
![](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2020.53.11.png)

Next, we’ll create a test table named todo_list. From the MySQL console, we will run the following statement:
```
CREATE TABLE example_database.todo_list (
 	item_id INT AUTO_INCREMENT,
 	content VARCHAR(255),
 	PRIMARY KEY(item_id)
 );
```
Insert a few rows of content in the test table. We might want to repeat the next command a few times, using different VALUES:
```
mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
```
To confirm that the data was successfully saved to your table, run:
```
mysql> SELECT * FROM example_database.todo_list;
```
This gave us the following output:
![](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2021.05.24.png)

After confirming that we have valid data in our test table, we can exit the MySQL console:
```
mysql> exit
```
Now we can create a PHP script that will connect to MySQL and query for our content. We will create a new PHP file in our custom web root directory using our preferred editor. We’ll use vi for that:
```
nano /var/www/projectLEMP/todo_list.php
```
The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.
We will copy this content into our todo_list.php script:

```
<?php
$user = "example_user";
$password = "password";
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

We will thereafter save and close the file after editing.
We can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:
```
http://<Public_domain_or_IP>/todo_list.php
```
We should see a page like this, showing the content we’ve inserted in our test table:
![](https://github.com/Omolade11/LempStack_AWS/blob/main/Images/Screenshot%202022-11-29%20at%2021.50.35.png)
That means our PHP environment is ready to connect and interact with our MySQL server.
 
