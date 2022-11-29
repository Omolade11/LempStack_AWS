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



