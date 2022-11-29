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
 
![SQL]()

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





