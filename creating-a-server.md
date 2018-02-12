## Creating your server
This tutorial will focus on creating a Ubuntu server as an droplet on the [Digital Ocean IaaS](https://www.digitalocean.com/), installing Node.js LTS and adding some security.
First of all there are some questions we have to answer:

### Why Ubuntu?
Ubuntu is a Linux distribution meaning that it is a flavor of Linux. Linux is very common to use when choosing an OS for your servers and ubuntu is one of the more common distributions. There are of course other distributions that also will work, like CentOS. But in this tutorial we will focus on Ubuntu.

### Why Digital Ocean
While waiting on having our own cloud solution we are bound to use one of the more common cloud providers. We could have used Amazon, Microsoft Azure, Heroku: etc. but since we are using GitHub and you are students at LNU we get a offer through your [Github Student Developer Pack](https://education.github.com/pack). Follow the link and claim your pack, it will give you information about how to register at Digital Ocean and use your "$50 in platform credit" meaning you could run a server for free during a time period. You are not forced to use Digital Ocean but most of the tutorials will use that provider. Feel free to choose another if you want.
So start of by creating your account, use the credit you get trough your student developer pack.

## Creating a server at Digital Ocean
Digital Ocean are using a concept called droplets. In our case a droplet is a virtual machine where we will run our application and servers. So lets start by creating a droplet. 

You have to specify a couple of things when creating your droplet:

1. We should use Ubuntu 16.04 as distribution. There are a couple  of one-click apps with stuff pre-installed but the node-versions they are using is pretty old. We will install newer node-version on a clean Ubuntu server.
2. It should be OK to choose the smallest size of the droplet (1GB, 1vCPU) just to run a simple web application. If you plan to add a database server into your droplet you may choose a bigger size.
3. We don´t need any "block storage", and none of the "additional options".
4. Choose a data center near to where you (or your visitors) are placed.
5. Under the "Add your SSH keys" you could add your own SSH keys. SSH keys is a better way of handling the login in phase to your droplet instead of using your username and password. If you want you could fix this by following these two guides that will give a deeper understanding of SSH keys and how to use them on different services:
  * For mac/linux users: https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets
  * For Windows users: https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-putty-on-digitalocean-droplets-windows-users 

 If you are unsure or want to take a easy road you could ignore SSH keys and just use the username:password way. This tutorial will use this way but if you use ssh-keys you won´t have any problem to follow along.

 When you have created your droplet (and not added a SSH-key) you will get an email with the IP-number of your droplet and the username and password you will use to login to it the first time.

 ### Login into your droplet
 When you have your IP-number and password you should be able to log into your droplet by using your terminal. So open your terminal and write the following command:

 ```
 ssh root@<your ip number>
 ```
 This will first of all ask you a question about the authenticity of the host. Write "yes" and then press Enter.

 The first time you log in to your droplet you are asked to change the password. First write your current password and then write the new one twice. Since we are now connected to a UNIX system you wont see anything when writing your passwords.

 If everything went alright you should be logged in as root on your Ubuntu server. 

### Changing the user
The root user on a Ubuntu is a very powerful user and it is advised that you don´t use this user when working with your server. Therefore we will add a new user that we will use instead. We also have to give this user some permissions to do stuff in the system so we can log in as this user when starting to administrate our server. We are going to create a new user with sudo permissions. To do this follow the following commands:

```
adduser thajo
```
in this case we create a new user named "thajo", of course you should give it a better suited username. When creating a new user you choose a good password and there will be some optional questions. Just hit enter to ignore them.

The next task is to add this new user to the sudo group meaning that this user have the extra permission needed. Write the following command to do this:

```
gpasswd -a thajo sudo
```
of course you change "thajo" to your chosen username.

Now lets try it by updating your server to the latest security fixes. Switch to the newly created user (thajo) in my case:

```
su thajo
```

Now run the following commands as this user to update the Ubuntu server (you may have to write your password)
```
sudo apt-get update
```
this will check for new updates (will write out a couple of things in the terminal). Then write:

```
sudo apt-get upgrade
```
This will install the found updates (you have to answer Y to the question)

### Login out as root and login in as your new user
OK, lets try to login as this new user. First of all we have to log out from the server. Press ctrl+c to first go back to the root user on your ubuntu server. Then press ctrl+c agin to log out of your Ubuntu server. You should now be on your local machine.

Lets try to login to your Ubuntu but using your newly created user (thajo in my case)

```
ssh thajo@<your ip number>
```
you should be able to login to your Ubuntu server by using the password you gave when creating this user. If this works we are all set up for installing Node.js on our server.

## Installing Node on a Ubuntu 16.04
First of all you could check that we don´t have Node installed by writing the command `node -v`. This should say something about that we don´t have Node installed yet. We are now going to install the LTS version of Node. When writing this it is version 8.9.4, but check this up on the Node home page.

To install the 8.x version of node do the following command:

```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
```
It will download and run a pre-installation script. To execute the installation do the following:

```
sudo apt-get install -y nodejs
```
This will install Node on the server. Try it by checking that you got the right version of Node and npm

```
node -v
```
and
```
npm -v
```

## Adding a firewall
The last step is adding a firewall to our server. A firewall is a security wall that will just allow a server to listen to the ports we are specifying. In our case we will only listen to port 22 (for our ssh connection), 80 (for HTTP) and 443(for our HTTPS). All other connections will be closed meaning that we have minimized the risk of having insecure port listening for connection that a attacker would try.

In Ubuntu there is a very simple firewall that gives us this feature. It is called UFW, Uncomplicated Firewall. Here is how we configure it.

First of all check if it is already active by writing the command
```
sudo ufw status
```
It will probably say that it is inactive meaning that we have no protection yet. Before anything else make sure that we will allow SSH when activating it. You don´t want to activate it and forget setting this!

```
sudo ufw allow ssh
```
The we should also allow the http and https ports

```
sudo ufw allow http
sudo ufw allow https
```
Now we can activate it by running 
```
sudo ufw enable
```
The check the status again
```
sudo ufw status
```
It should say that the Status is active and that the ports 22, 80 and 443 is allowed from "Anywhere". Try it by log out from the server and log in again.

### References for more information about the above
* https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2
* https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-16-04
* https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04
* https://help.github.com/articles/connecting-to-github-with-ssh/
