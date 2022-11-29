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
