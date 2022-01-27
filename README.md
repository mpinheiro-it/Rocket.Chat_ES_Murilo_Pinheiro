# :rocket: Technical Support Challenge

<img src="https://itcraftapps.com/wp-content/uploads/2019/05/rocket-1.png" height="600px">

## Index
1. Accessing the Environment
2. Results
3. Deployment Choice
4. Installation Process
5. Additional
<br>

## 1. Accessing the environment

To access my Rocket.Chat environment please use the information below:

| Resouce | Details |
| ------------- | ------------- |
| URL  | www.murilo.tec.br  |
| Username:  | test.user  |
| Password:  | 940TMj$MNoh_1/#Z  |
<br>

## 2. Results
Below you will find the results of the requested tests:
  
  <details><summary>1. Create a new user via an API endpoint</summary><br>
    <p>Admin user:</p>
    <img src="https://github.com/mpinheiro-it/Rocket.Chat_ES_Murilo_Pinheiro/blob/main/0-Creating-New-Admin-User.png" height="400px">
    <p>Regular user:</p>
    <img src="https://github.com/mpinheiro-it/Rocket.Chat_ES_Murilo_Pinheiro/blob/main/01-Creating-New-User.png" height="400px">
    <p>Regular user logged in:</p>
    <img src="https://github.com/mpinheiro-it/Rocket.Chat_ES_Murilo_Pinheiro/blob/main/1-New-User-Logged-In.png" height="400px" width="1000px">    
  </details>
    <br>
    
  <details><summary>2. Get the room information via an API endpoint</summary><br>    
    <img src="https://github.com/mpinheiro-it/Rocket.Chat_ES_Murilo_Pinheiro/blob/main/2-Room-Information.png" height="400px">    
  </details><br>
  
   <details><summary>3. Get a list of all user roles in the system via an API endpoin</summary><br>    
    <img src="https://github.com/mpinheiro-it/Rocket.Chat_ES_Murilo_Pinheiro/blob/main/3-List-Of-Users-And-Roles.png" height="400px">
   </details><br>

## 3. Deployment Choice

I have chosen to deploy my server using an AWS EC2 instance due to the following reasons:

- To have full control over my deployment.
- It was a good opportunity to practice using the AWS DNS service, named Route 53. 
- I could use a personal domain I already own for hosting the web server.
- It also allowed me to generate a SSL certificate for using HTTPS.
<br>

## 4. Installation Process

<details><summary>Launch an AWS EC2 Instance</summary>
<p><br>
Log into AWS console, open the "EC2" service, click on "Instances" in the left sidebar and click on "Launch Instance" to setup a new EC2 instance. Now follow the steps below:
<li>In the first step search for "Ubuntu Server 18.04 LTS" with "64-bit (x86)" architecture and click on "Select"</li>
<li>Select an instance type of your choice and click "Next"</li>
<li>Adjust the instance details as needed or keep the defaults. Proceed with "Next"</li>
<li>Adjust the storage size and configuration as needed and click on "Next"</li>
<li>Make sure to add a tag called "Name" and assign a value</li>
<li>Allow "SSH", "HTTP" and "HTTPS" in the security group configuration, proceed with "Review and Launch"</li>
<li>Review your instance configuration and confirm with "Launch"</li>
<li>Choose an existing key pair or create a new one and click on "Launch Instance"</li>
</p>
</details>

<details><summary>Allocate An Elastic IP</summary>
<p><br>
Back in the "EC2" service dashboard, click on "Elastic IPs" in the left sidebar:
<li>Click on "Allocate New Address"</li>
<li>Select "Amazon's pool of IPv4 addresses" and click on "Allocate"</li>
<li>Click on the newly created IP address and select "Associate Elastic IP address"</li>
<li>Select your instance and click "Associate"</li>
<li>In the details below, copy the "Public DNS". You will need it in the DNS step.
(It should be in a format like this: ec2-18-197-161-168.eu-central-1.compute.amazonaws.com)</li>
</p>
</details>

<details><summary>Configure DNS on Route 53</summary>
<p><br>
Open the "Route 53" service dashboard:

<li>Create a new hosted zone by clicking on "Create Hosted Zone":</li>
<li>Enter your domain name and select "Public Hosted Zone" as type, then click on "Create"</li>
<li>Select your newly created zone and click on "Create Record Set"</li>
<li>Enter "www" as subdomain (if desired), select Type "CNAME", enter the Public DNS name from the above step to the value field and click "Create"
Click on "Allocate New Address"</li>
</p>
</details>

<details><summary>Add the AWS DNS server to your Domain registrar</summary>
<p><br>
Once you finish creating your "Route 53" hosted zone, the dashboard should display the AWS DNS servers for which the trafic should be routed to:
<img src="https://github.com/mpinheiro-it/Rocket.Chat_ES_Murilo_Pinheiro/blob/main/DNS-Servers.png">

These servers should be added as DNS servers for your domain.

In my case, my domain registrar is Registro.br. I accomplished this by following the steps below:

<li>Log in to registro.br with my personal account and selecting the desired domain. </li>
<li>Navigate to the "DNS" section of the page and click on "Change DNS Servers"</li>
<li>Add each one of the servers provided by AWS on the list and click "ok"</li>
<li>It will probably be needed to wait for a few hours for the changes to take place</li>
</p>
</details>

<details><summary>Get an SSL certificate from Let's Encrypt</summary>
<p><br>
  
<li>Access the EC2 instance via SSH:  </li>
  
 ```
  ssh -i <path_to_key_file.pem> ubuntu@<public_ip_address> 
 ```  

Install certbot using apt:
  
  ```
 sudo apt update
 sudo apt install certbot  
  ```
 
Obtain certificate from Let's Encrypt:
  
   ```
  sudo certbot certonly --standalone --email <emailaddress@email.com> -d <domain.com> -d <subdomain.domain.com>
  
  ```
</p>
</details>
  

<details><summary>Set up a Web Server using Nginx</summary>
<p><br>
  
Install Nginx web server:
  
 ```
  sudo apt-get install nginx
 ```  

Backup the default config file for reference:
  
  ```
 cd /etc/nginx/sites-available
 sudo mv default default.reference 
  ```
 
Create a new site configuration for Rocket.Chat:
  
   ```
  sudo nano /etc/nginx/sites-available/default
  
  ```
The configuration file should contain these details listed below. Make sure to replace ABC.DOMAIN.COM with your domain (it appears 4 times). Make sure to update it in the path to your key files as well.
  
  ```
   server {
     listen 443 ssl;

     server_name <ABC.DOMAIN.COM>;

     ssl_certificate /etc/letsencrypt/live/<ABC.DOMAIN.COM>/fullchain.pem;
     ssl_certificate_key /etc/letsencrypt/live/<ABC.DOMAIN.COM>/privkey.pem;
     ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
     ssl_prefer_server_ciphers on;
     ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

     root /usr/share/nginx/html;
     index index.html index.htm;

     # Make site accessible from http://localhost/
     server_name localhost;

     location / {
         proxy_pass http://localhost:3000/;
         proxy_http_version 1.1;
         proxy_set_header Upgrade $http_upgrade;
         proxy_set_header Connection "upgrade";
         proxy_set_header Host $http_host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header X-Forwarded-Proto http;
         proxy_set_header X-Nginx-Proxy true;
         proxy_redirect off;
     }
 }

 server {
     listen 80;

     server_name <ABC.DOMAIN.COM>;

     return 301 https://$host$request_uri;
 }
  
  ```

Test the Nginx configuration to make sure there are no syntax errors:
  
   ```
   sudo nginx -t
   ```
  
If the syntax test went successful, restart Nginx:
  
  ```
  sudo systemctl restart nginx
  ```
Confirm that it is running properly by opening a web browser and going to your domain name. You will get a page stating "502 Bad Gateway". This is expected, since the Rocket.Chat backend is not yet running. Make sure the SSL connection is working properly by clicking the lock icon next to the address bar, make sure it's valid and issued by "Let's Encrypt Authority X3". 
</p>
</details>

<details><summary>Install Docker and Docker Compose</summary>
<p><br>
  
Install Docker (and any dependencies):
  
 ```
 sudo apt-get update
 sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
 sudo apt-key fingerprint 0EBFCD88
 # confirm the fingerprint matches "9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88"
 sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
 sudo apt-get update
 sudo apt-get install docker-ce docker-ce-cli containerd.io
  
 ```  

Install docker-compose:
  
  ```
 sudo curl -L "https://github.com/docker/compose/releases/download/1.26.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
 sudo chmod +x /usr/local/bin/docker-compose
  ```
 
</p>
</details>
  
<details><summary>Set up the Docker Containers</summary>
<p><br>
  
Create local directories:
  
 ```
 sudo mkdir -p /opt/docker/rocket.chat/data/runtime/db
 sudo mkdir -p /opt/docker/rocket.chat/data/dump
  
 ```  

Create the docker-compose.yml file:
  
  ```
  sudo nano /opt/docker/rocket.chat/docker-compose.yml
  ```

Add the configuration details below. Make sure to replace ABC.DOMAIN.COM with your actual domain name again:
  
  ```
   version: '2'

 services:
   rocketchat:
     image: rocket.chat:latest
     command: >
       bash -c
         "for i in `seq 1 30`; do
           node main.js &&
           s=$$? && break || s=$$?;
           echo \"Tried $$i times. Waiting 5 secs...\";
           sleep 5;
         done; (exit $$s)"
     restart: unless-stopped
     volumes:
       - ./uploads:/app/uploads
     environment:
       - PORT=3000
       - ROOT_URL=https://<ABC.DOMAIN.COM>
       - MONGO_URL=mongodb://mongo:27017/rocketchat
       - MONGO_OPLOG_URL=mongodb://mongo:27017/local
     depends_on:
       - mongo
     ports:
       - 3000:3000

   mongo:
     image: mongo:4.0
     restart: unless-stopped
     command: mongod --smallfiles --oplogSize 128 --replSet rs0 --storageEngine=mmapv1
     volumes:
       - ./data/runtime/db:/data/db
       - ./data/dump:/dump

   # this container's job is just to run the command to initialize the replica set.
   # it will run the command and remove himself (it will not stay running)
   mongo-init-replica:
     image: mongo:4.0
     command: >
       bash -c
         "for i in `seq 1 30`; do
           mongo mongo/rocketchat --eval \"
             rs.initiate({
               _id: 'rs0',
               members: [ { _id: 0, host: 'localhost:27017' } ]})\" &&
           s=$$? && break || s=$$?;
           echo \"Tried $$i times. Waiting 5 secs...\";
           sleep 5;
         done; (exit $$s)"
     depends_on:
     - mongo
  ```
  
Start containers:
  
  ```
 cd /opt/docker/rocket.chat
 sudo docker-compose up -d
  ```

Wait a bit for the replica set to be initialized for MongoDB (about 30-60 seconds) and confirm Rocket.Chat is running properly:
  
  ```
  sudo docker-compose logs -f rocketchat
  ```
</p>
</details>
  
<details><summary>Use it!</summary>
<p><br>
  
Login to your site. In my case, it is https://www.murilo.tec.br.  
Note: the first user to login will be an administrator user.

</p>
</details>
<br>
   
## 5. Additional
  
