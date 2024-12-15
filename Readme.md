## DOCUMENTATION
This documentation is about how I provisioned my server, installed the web server, deployment of the HTML page and configured networking step by step.

### Provisioning of the Server
Using virtualization to set up a Linux server,

- I installed a Virtual Machine (Virtual Box) on my Host Machine (Windows),

- I downloaded and installed `vagrant_2.4.1_windows_amd64` which is meant for windows,

- Using Termius, I created a repository (mkdir repo) and cd into the repository to set up vagrant with Ubuntu 20.04 LTS also called focal 64 (an ISO image/Linux distribution),

- I ran the command `vagrant init focal/64` to initialze the vagrantfile inside the repository created and in the virtual box,

- Then I ran command `vagrant up` to start up my server (Ubuntu 20.04 LTS) which is now being installed inside my virtual box,

- Then command `vagrant ssh` to enter the server.

*My server is reeady for use*

### Installation of the Web Server
After the provisioning of the server (still inside Termius),

- I ran the command `sudo apt update` to update and ready all new available applications,

- Then I ran `sudo apt install apache2` to install the Apache2 web server.

*Apache2 is ready for hosting*

### Deployment of the HTML Page
- Using VS code, I wrote a simple HTML page and copied and pasted it into /home/vagrant/second_semester_project/BasitAdebayo.html inside my linux server,

- Inside my Linux Server, I copied the content of my HTML page into the apache test file (/var/www/html/index.html) using the command `cp /home/vagrant/second_semester_project/BasitAdebayo.html /var/www/html/index.html`,

- I ran `ifconfig` command to see the public IP address, which is inet **192.168.56.3** founder under the enp0s8,

- Then I pasted the public IP address inside my browser which did not show the content of the HTML page due to the network not being configured.

### Networking Configuration
I configured the networking with the process below:

- Ran command `sudo apt install ufw`,

- I ran `sudo ufw app list` to see the list of services allowed in and out of my linux server, Apache was part of it,

*Adding the required ports to allow communication for apache2*

- I ran `sudo ufw allow 22` to allow commication from my local machine,

- I ran `sudo ufw allow 80` to allow connection from "HTTP",

- I ran `sudo ufw allow 443` to allow connection from "HTTPS",

*Configuration of HTTPS for Apache2 Web Server using Self-signed SSL Certificate*
- I ran the command `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/selfsigned.key -out /etc/ssl/certs/selfsigned.crt` to generate the selfsigned certificate, and I filled in the necessary details with the "Common Name" being my IP address (192.168.56.3),

- Then I configure apache to use the selfsigned certificate by running command `sudo a2enmod ssl` to enable ssl module and `sudo systemctl restart apache2` to restart my apache for the changes to be effective,

- I edited the apache configuration file with the command `sudo nano /etc/apache2/sites-available/default-ssl.conf` and updated the file,

- I enabled the SSL site configuration by running the command `sudo a2ensite default-ssl.conf` and reload apache2 with command `sudo systemctlreload apache2`,

- I ran command `sudo apacectl configtest` to check if Apache runs correctly, then I restartit again with `sudo systemctl restart apache2`,

- Then on my browser, I input `https://192.168.56.3` to load my HTML page.

*Configuring Apache Server to Allow External Access Using Tunneling Service (ngrok)*
I ran the following command:

- `sudo apt install snapd`,

- `sudo snap install ngrok` to install ngrok via snap,

- `cd /tmp` to navigate to a directory for download,

- `wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz` to fetch the ngrok binary archive,

- `tar -xvzf ngrok-v3-stable-linux-amd64.tgz` to extract the ngrok binary,

- Moved the binary to a system path using `sudo mv ngrok /usr/local/bin` to ensure that the command is available for system-wide,

- `ngrok version` to verify the installation.

- Signed up for a free account on Ngrok.com and copied my **authtoken** from my dashboard,

- To link my installation to my account, I ran command `ngrok config add-authtoken *my_authtoken*`,

- I started the tunnel by running command `ngrok http 80`

  It showed up an interface with a public IP URL being displayed like;

  Forwarding https://fdf0-197-211-52-193.ngrok-free.app -> http://localhost:80,

  The public URL is "https://fdf0-197-211-52-193.ngrok-free.app" which can be used to access my apache web server externally,

- To tunnel HTTPS directly, I used `ngrok http https://localhost`,

- "ctrl+c" to stop the tunnel.

**Note that: the Ngrok session terminate after 2 hours,**

**Public URLs are random and change each time I start ngrok,**

**i.e My Apache2 web server can only be accessed when I start ngrok session from my local machine and I provide a new  public URL as it changes every time I start the tunnel**


### The Public IP Address and How to Run the Web Server
**Public Ip address: `192.168.56.3`**

**How to run the Web server**
- Option 1: Input `https://192.168.56.3` into a web server, which can only be done on my local machine
- Option 2: Accessing the web server externally using ngrok public URL
    I need to start my ngrok tunnel from my end using command `ngrok http 80` and provide the public URL generated to the external user.
    Then the user can paste the link into his/her browser to access the web server
**Note: I need to be contacted to start the server from my end**
  *Contact: bahseet2022@gmail.com*


**I used virtualization because I was having issues connecting to my EC2 instances on AWS which made me delete my AWS account, and I can not open another account with my details until I have been cleared which takes 90 days duration.**
