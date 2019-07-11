# Multi-Use Home Server Project
The purpose of this repository is to document and share my experience with building a multi-purpose Linux server.
My plan for this server is to use it as a web server and a cloud file server. 
I will be using **Fedora 30 Server** edition on a scrapped computer I build and deploying it in my household. 

# Table of Contents
1. [ Description. ](#desc)
2. [ Usage tips. ](#usage)

<a name="desc"></a>
# Writing the ISO & Installing onto the machine
I retreieved the ISO image from the official Fedora website *https://getfedora.org/en/server/download/* and used a tool called **Rufus**.
Rufus is an easy to use ISO image writer *https://rufus.ie/*. After writing the ISO to a 4GB flash drive, I inserted it into my machine and followed the installation GUI. 

<a name="usage"></a>
# Post Installation
To access the server remotely and from other computers localy we will need a SSH (Secure Shell) client. 
Fedora 30 Server eddition already comes with a SSH client instlled on the computer itself but this is on a known port, port 22. This leads me to my first security measure on this server.
I will be using a less known port (ex. **56358**) to secure the SSH service. To do this you will need to navigate to the where the SSH config file is located and edit the configuration file with your favorite text editor. 
```bash
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
