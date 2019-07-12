# Multi-Use Home Server Project
The purpose of this repository is to document and share my experience with building a multi-purpose Linux server.
My plan for this server is to use it as a web server and a cloud file server. 
I will be using **Fedora 30 Server** edition on a scrapped computer I build and deploying it in my household. 

# Table of Contents
1. [ Writing the ISO & Installing onto the machine ](#desc)
2. [ Securing SSH ](#SSH)
3. [ NGINX Web Server](#NGINX)
4. [ Own Cloud](#owncloud)
<a name="desc"></a>
# 1. Writing the ISO & Installing onto the machine
I retreieved the ISO image from the official Fedora website *https://getfedora.org/en/server/download/* and used a installation tool called **Rufus**.
Rufus is an easy to use ISO image writer, found at *https://rufus.ie/*. After writing the ISO to a 4GB flash drive, I inserted it into my machine and followed the Fedora installation GUI. 

<a name="SSH"></a>
# 2. Securing SSH
To access the server remotely and from other computers localy we will need a SSH (Secure Shell) client. 
Fedora 30 Server eddition already comes with a SSH client instlled on the computer itself but this is on a known port, port 22. This leads me to my first security measure on this server.
I will be using a less known port (ex. **56358**) to secure the SSH service. To do this you will need to navigate to the where the SSH config file is located and edit the configuration file with your favorite text editor. 
```bashPost Installation
sudo nano /etc/ssh/ssh_config
```
![alt text](https://github.com/collinkleest/HomeServer/blob/master/images/Capture.JPG)
Now we must check if we have a firewall running. Fedora comes with firewalld by default so we will run some bash to make sure it's running and then set a rule to allow traffic to hit our SSH service.
```bash
sudo systemctl start firewalld
sudo systemctl status firewalld
#This should output as running
```
Now I will add a rule to allow traffic on 56358
```bash
#Check which zone is active on network interface
sudo firewall-cmd --get-active-zones
#Add rule to that zone and port 56358
sudo firewall-cmd --zone=FedoraServer --permanent --add-port=56358/tcp
```
##### Allowing SSH to be accessible from anywhere
To allow SSH to be accessible from anywhere in the world we will need to port forward to our machine. You will need to log into your default gateway (router).
```bash
#issue this command to find the IP  of the default gateway
ip route | grep 'default'
```
The IP returned will be the defualt gateway, and can be accessible from a browser. Simply type the default gateway into your browser and you will be redirected to the your routers page. NoIP has a great guide to portforward **https://www.noip.com/support/knowledgebase/general-port-forwarding-guide/** 
<a name="NGINX"></a>
# 3. NGINX Web Server
After hours of deciding between NGINX & Apache, I decided on NGINX because of how powerfully fast it is when using static configurations. For now my plan is to host my portfolio on this server so NGINX should suit be fine for my use case, but if you are using a dynamic configuration with mutiple web pages Apache may be better for you.
I will now go through my installation and configuration of NGINX. This will be different across distrobutions of linux, as I said before I am using Fedora so this installation will work for most RedHat distribtuions.  
```bash
#install nginx through dnf
sudo dnf -y install nginx
#start & enable nginx on startup
sudo systemctl start nginx
#clarify nginx is running 
systemctl status nginx 
sudo systemctl enable nginx
```
##### Test Your NGINX Server
```bash

<a name="owncloud"></a>
# 4. Own Cloud Personal Cloud Server 
