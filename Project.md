# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

Project Prequisite is AWS EC2 instance.

## Steps Involved
1.   INSTALLING THE NGINX WEB SERVER
2.   INSTALLING MYSQL
3.   INSTALLING PHP
4.   CONFIGURING NGINX TO USE PHP PROCESSOR
5.   TESTING PHP WITH NGINX

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
