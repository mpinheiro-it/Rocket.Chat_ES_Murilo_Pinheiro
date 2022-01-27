# :rocket: Technical Support Challenge

<img src="https://itcraftapps.com/wp-content/uploads/2019/05/rocket-1.png" height="600px">

## Index
1. Accessing the Environment
2. Deployment Choice
3. Installation Process
4. Results
5. Additional

## 1. Accessing the environment

To access my Rocket.Chat environment please use the information below:

| Resouce | Details |
| ------------- | ------------- |
| URL  | www.murilo.tec.br  |
| Username:  | test.user  |
| Password:  | 940TMj$MNoh_1/#Z  |

## 2. Deployment Choice

I have chosen to deploy my server using an AWS EC2 instance due to the following reasons:

- To have full control over my deployment.
- It was a good opportunity to practice using the AWS DNS service, named Route 53. 
- I could use a personal domain I already own for hosting the web server.

## 3. Installation Process

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


These servers should be added as DNS servers for your domain.

In my case, my domain registrar is Registro.br. I accomplished this by:

<li>Log in to registro.br with my personal account and selecting the desired domain. </li>
<li>Navigate to the "DNS" section of the page and click on "Change DNS Servers"</li>
<li>Add each one of the servers provided by AWS on the list and click "ok"</li>
<li>It will probably be needed to wait for a few hours for the changes to take place</li>
</p>
</details>
