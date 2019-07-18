# Multi-Use Home Server Project
The purpose of this repository is to document and share my experience with building a multi-purpose Linux server. I will cover many topics of building the server, including building the server from scratch, implementing security measures, configuring a web server, configuring a dynamic IP client, and building a personalized cloud. 
My plan for this server is to use it as a web server and a cloud file server for my own personal needs. 
I will be using **Fedora 30 Server** edition on a scrapped computer I build and deploying it in my household. This isn't a tutorial but documentation to enhance my learning experience throughout the process of building the server. 

# Table of Contents
1. [ Writing the ISO & Installing onto the machine ](#desc)
2. [ Securing SSH ](#SSH)
3. [ NGINX Web Server](#NGINX)
4. [ Dynu Dynamic IP Client](#dynu)
5. [ OwnCloud](#owncloud)

<a name="desc"></a>
# 1. Writing the ISO & Installing onto the machine
I retreieved the ISO image from the official Fedora website *https://getfedora.org/en/server/download/* and used a installation tool called **Rufus**.
Rufus is an easy to use ISO image writer, found at *https://rufus.ie/*. After writing the ISO to a 4GB flash drive, I inserted it into my machine and followed the Fedora installation GUI.

<a name="SSH"></a>
# 2. Securing SSH
To access the server remotely and from other computers localy we will need a SSH (Secure Shell) client.
Fedora 30 Server edition already comes with a SSH client instlled on the computer itself but unfortunetly this client runs on a well known port, port 22. This leads me to my first security measure on this server.
I will be using a less known port (ex. **56358**) to secure the SSH service. To do this you will need to navigate to the where the SSH config file is located and edit the configuration file with a text editor, I will use nano but there are other good options like vi, or vim. 
```bash
#Directign nano to the ssh configuration file
sudo nano /etc/ssh/ssh_config
```
Here I comment out the port and use a less known port. 
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
The IP returned will be the defualt gateway, and can be accessible from a browser. Simply type the default gateway into your browser and you will be redirected to the your routers page. NoIP has a great guide to portforward: *https://www.noip.com/support/knowledgebase/general-port-forwarding-guide/*

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

```bash
#Add HTTP & HTTPS to Server
sudo firewall-cmd --zone=Fedora Server --add-service=http --permanent
#Add HTTPS
sudo firewall-cmd --zone=Fedora Server --add-service=https --permanent
```

### Test Your NGINX Server
Open up a browser on a host and hit the ip address of your server followed by a colon and the port which it is running on. By default this is port 80 (ex. 192.168.0.92:80). If you are presented with the following page or something similar to this you made successfully deployed NGINX.  
![alt text](https://github.com/collinkleest/HomeServer/blob/master/images/Capture2.JPG)
I now copy my portfolio files over to my system using a windows tool called WinSCP, a easy to use tool for transfering files over SFTP. A download for WinSCP can be found here *https://winscp.net/eng/index.php*.

### Configuring SSL (https)
Today, almost all websites have an SSL cetificate, HTTPS allows for encyrpted data transfer between the user and the server. Although my website won't use HTTPS at first, it may come in handy in the future.
I will use a service called **Let's Encrypt**, this is a free service for SSL certificiates.

```bash
#Install certbot for nginx
sudo dnf -y install certbot certbot-nginx
#Automatically allow cetbot to edit your nginx confitguration file
sudo certbot --nginx
```

<a name="dynu"></a>
# 4. DYNU Dynamic Update Client
I downloaded the rpm file from *https://www.dynu.com/Downloads/IP-Update-Client-For-Linux* and transfered the rpm file to the server via sftp. Below are the commands for instaling the RPM.
```bash
#Install RPM
sudo rpm -i dynuiuc-2.6.2-2.el7.x86_64.rpm
#Edit configuration file using username and password from DYNU's website
sudo nano /etc/dynuiuc/dynuiuc.conf
```
<a name="owncloud"></a>
# 5. Own Cloud Personal Cloud Server
