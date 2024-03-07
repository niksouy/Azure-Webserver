<h1>Build, Secure, and Protect a Web Application</h1>
<h2>Description</h2>
This was my first deliverable Cybersecurity project during studying at UofT through which I built, secured, and protected a cloud application that hosts my own cyber blog (alicyberblog.info). The porpous of this project was to demontrate my knowledge of cloud, cloud security, networking, web development, cryptography, and network security.<br />


<h2>Utilities Used</h2>

- <b>Microsoft Azure (Virtual Network, NSG, WAF, App service)</b></b> 
- <b>GoDaddy</b>

<h2>Environments Used </h2>

- <b>A personal Azure account </b>

<h2>Implementing a Virtual Network in Azure :</h2>

<p align="center">
⬇️Step-1: Setting up the Resource Group  
<br/>
<img src="https://i.imgur.com/NERx2N5.png" height="80%" width="80%" />
<br />
- <br />
⬇️Step-2: Creating a Virtual Network with the Resource Group, built on step-1  
<br/>
<img src="https://i.imgur.com/7NrWL8w.png" height="80%" width="80%" />
<br />  
<br />  
<img src="https://i.imgur.com/MBbZ5an.png" height="80%" width="80%" />
<br />
<br />  
<img src="https://i.imgur.com/oUKRjbq.png" height="80%" width="80%" />  
<br />
- <br />
⬇️Step-3: Creating 3 VMs inside the Vnet that I made in step-2, including 2 VMs to host my Blog as a webserver, and it's redundant, alongside 1 JumpBox to control and deploy those servers. 
<br />
<img src="https://i.imgur.com/yK3kqpp.png" height="80%" width="80%" />
<br />
<br />
Finalizing the setup of the JumpBox Provisioner with the PUBLIC IP in order to be routable from anywhere.
<br/>
<img src="https://i.imgur.com/CeXnDUY.png" height="80%" width="80%" /> 
<br />
<br />
✒️Setting up 2 more VMs with PRIVATE IP and the names of "Web-1" and "Web-2. I will communicate with these VMs via the JumpBox. 
Generating SSH Public Keys for these VMs to establish SSH connection to these webservers followed by inputting these encrypted keys to the JumpBox. By this measure, JumbBox would be the sole gateway to communicate with my webservers.
<br/>
<img src="https://i.imgur.com/eTv4n7t.png" height="80%" width="80%" /> 
<br /> -
- <br />
🧱Defining security group to Azure firewall to manage access to the VMs. I created an inbound rule to do SSH from anywhere to JumpBox based on its assigned Public IP, while I restrict access to it based on SSH service, TCP protocol, and port 22. 
<br />
<img src="https://i.imgur.com/lfQenzT.png" height="80%" width="80%" />
<br />
<br /> 


  
<h2>Making JumpBox ready to deploy webservers through containerization method:</h2>
<p align="center">
⬇️Step-4: configure the JumpBox to run Docker containers and installing a container. After doing SSH to the JumpBox which is a Linux machine, I did the below process in order to pullup the proper Ansible into the Jumpbox for my deployment.
<br />
<br /> 
  
1. Installing docker.io on the Jump box
2. Verifying that the Docker service is running
3. pulling the container provided by UofT Cybersecurity Bootcamp cyberxsecurity/ansible
4. Launching the Ansible container and connecting to it using the appropriate Docker commands
  
<br />
<br />
<img src="https://i.imgur.com/I6PbVUm.png" />
<br />
<br />  
<img src="https://i.imgur.com/QikIlGt.png" />
<br />
<br /> 

5. Creating a new security group rule allowing JumpBox full VNet access. This is internal communication and works with Private IPs; as in this case, after establishing an SSH connection to JumpBox by its Public IP, JumpBox would use its Private IP to communicate with web servers. So we need another Security group and inbound rule within the Azure firewall just for internal SSH communication between the JumpBox and the webservers.
<br />
<br />
6. For securing the webservers from outsiders and for making the JumpBox the sole approved way of communication, I generated public-key for the Ansible container within the JumpBox as below
<br />
<br />
<img src="https://i.imgur.com/ZaYCdt5.png" height="80%" width="80%" /> 
<br /> -
- <br />
<br />
7. Now, by adding the container's public key within the Azure webservers through the Azure control panel for both webservers, I addressed those VMs to only accept the SSH inquiry that is coming from the intended source. After this update I could ping both VMs by their Private IPs, to verify the SSH-Key deployment.
<br />
<br />
<img src="https://i.imgur.com/MQ5IXqJ.png" height="80%" width="80%" /> 
<br /> -
- <br />
8. Now, it's time to configure Ansible's 2 config files to address the Docker Container with proper destination addresses and the Python interpreter command line.
<br />
<br />
<img src="https://i.imgur.com/ndqanRG.png" height="80%" width="80%" />
<br />
<br />-
📜Webservers IPs are 10.0.0.6 and 7, while the private IP for JumpBox is 10.0.0.4.
<br />
<img src="https://i.imgur.com/F96tKlO.png" height="80%" width="80%" />
<br />
<br />
Uncommented the highlighted line and replaced "root" username with my username within the VMs which is "sysadmin"
<img src="https://i.imgur.com/MuqW5JI.png" height="80%" width="80%" />
<br />
<br />
<h2></h2>
<p align="center">
⬇️Step-5: Created an Ansible playbook within the installed Docker on JumpBox and configured VMs with the DVWA web app.
<br />
<br /> 
  
1. Created an Ansible YAML file inside the installed Docker within the JumpBox to dictate and automate the deployment of the DVMA web app- UofT Bootcamp provided this configuration.  
<img src="https://i.imgur.com/a9sUQEh.png" />
<br />
<br />
2. ansible-playbook /etc/ansible/pentest.yml; that was the command I ran after configuring the YAML file.
<img src="https://i.imgur.com/8L4f8ho.png" height="80%" width="80%" />
<br />
<br />
3. To test that DVWA is running on the new VM, I did SSH to the new VM from the Ansible container.
<img src="https://i.imgur.com/W3RqiaJ.png" height="80%" width="80%" />
<br />
<br />
<h2></h2>

⬇️Step-6: Guaranting High-Availability through deploying a Load Balancer and a redundant webserver (IP 10.0.0.5) for the 2 webservers
<br />
<br />
1. Created a load balancer in the resource group I created in Step-1.
<img src="https://i.imgur.com/X9J44EV.png" height="80%" width="80%" />
<br />
<br />
2. Added frontend Public IP to the LoadBalancer since it's a gateway for the webservers and my URL, alicyber.blog, will be assigned to this valid IP; therfore, it should be routable from anywhere on internet and by anyone.
<img src="https://i.imgur.com/lWyIt72.png" width="80%" />
<br />
<br />
3. After that I define the 2 webservers to the Backend pool as they were the main host for my Blog.
<img src="https://i.imgur.com/quWJ51I.png" height="80%" width="80%" />
<br />
<br />
4. Created a load balancing rule to forward inquiries to frontend IP to port 80 of the Backend Pool.
<img src="https://i.imgur.com/vn75Lr2.png" height="80%" width="80%" />
<br />
<br />
5. For the last move in this step, I created a new security group rule to allow port 80 traffic from the internet to the internal VNet in order to reach the LoadBalancer. After configuring the secutiy rule now I could launched the sample DVWA website by typing the LoadBalancer's IP from my personal browser.
<img src="https://i.imgur.com/rqmHFqy.png" height="80%" width="80%" />
<br />
<br />
<br />
<br />
<h1>Final Scheme of My Azure Vnet including my webservers</h1>
<img src="https://i.imgur.com/6dY1cQe.png" height="120%" width="120%" />
<br />
<br />
<br />
<br />
<h1>Implemintation of "alicyberblog" Azure web application</h1>


<h2>Utilities Used</h2>

- <b>Azure Vnet that I built through Step-1 to Step-6</b></b> 
- <b>"alicyberblog.info" domain with GoDaddy</b>
<h2>Implementing a Virtual Network in Azure :</h2>
<br/ >
⬇️Step-1: Created "alicyberblog" web app with following configuration and based on a Apachi webserver   
<br/ >
<br/ >
<br/ >
<br/ >
<img src="https://i.imgur.com/ZaFzrD9.jpeg" height="80%" width="80%" />
<br />
📜✒️Also I took B1 option as the intended Pricing Plan
<br />
<br />
- <br />
- <br />
⬇️Step-2: customizing my domain within the Azure's App Service   
<br/ >
<br/ >
<img src="https://i.imgur.com/FUsdXcx.jpeg" height="80%" width="80%" />
- <br />
- <br />
⬇️Step-3: Deployed Apachi webserver on my web app. UofT Bootcamp provided us with a proper Docker Container (cyberxsecurity/project1-apachewebserver).  
<br/ >
<br/ >
1. To start deployment with provided container, I opened Azure Cloud Shell and selected "Bash" interface created a new storage upon asking:
<br/ >
<br/ >
<img src="https://i.imgur.com/JsmBWEj.png" height="80%" width="80%" />
<br/ >
<br/ >
2. I ran "az webapp config container delete" to delete my web app container’s settings.
<br/ >
<br/ >
3. To configure my web app with the provided container, I ran the following: az webapp config container set --name alicyberblog.info --resource-group RedTeam --docker-custom-image-name cyberxsecurity/project1-apachewebserver --enable-app-service-storage -t
<br/ >
<br/ >
4. To verify that the container has been added correctly, I ran the following command to show the container for my web app: az webapp config container show --name alicyberblog.info --resource-group RedTeam
<br/ >
<br/ >
5. Now, I could lunch my webapp from any device as a visitor, with a default look though🤣
<br />
<br />
<img src="https://i.imgur.com/o0wmb7h.png" height="80%" width="80%" />
<br />
<br />
6. As the final measaure I cusomized the look of my bloge by changing its HTML code based on my prefrences.
<br />
<br />
<img src="https://i.imgur.com/hMSTcAg.jpeg" height="150%" width="150%" />
<br />
<br />
7. In order to have access to my HTML panel I should have do SSH to the container through my Web App panel in Azure.
<br />
<br />
<img src="https://i.imgur.com/lR1bAaS.png" height="150%" width="150%" />
- <br />
- <br />
⬇️Step-4: Securing alicyberblog.info with SSL Certificates  
<br/ >
<br/ >
1. Created an Azure Key Vault and selected proper Access Policy
<img src="https://i.imgur.com/7dMprX7.png" height="80%" width="80%" />
<br/ >
<br/ >
Created a Self-Signed Certificate by OpenSSL through the "Bash" shell within the Azure portal
<img src="https://i.imgur.com/JsmBWEj.png" height="80%" width="80%" />
2. I ran this command to generate an encypted key in my Key Vault: 
<br/ >
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout project1_key.key -out project1_cert.crt -addext "extendedKeyUsage=serverAuth"
<br/ >
<br/ >
3. Created PFX format for the generated key and certificate by running this command:
<br/ >
openssl pkcs12 -export -out project1_cert.pfx -inkey project1_key.key -in project1_cert.crt

<img src="https://i.imgur.com/7SOQkP1.jpeg" height="80%" width="80%" />
<br/ >
4. Downloaded and added "project1_cert.pfx" into my Azure Key Vault
<br/ >
<img src="https://i.imgur.com/k37Ue1u.png" height="80%" width="80%" />
<br/ >
5. Then I went to the "Certificate" tab under App Sercices section, an uploaded the PFX file as an intented cryptographic key.
<br/ >
<img src="https://i.imgur.com/KYhMKLh.png" height="80%" width="80%" />
<br/ >
6. Now it was a time to bind "alicyberblog.info with the uploaded PFX file within the Certificate tab. To do so, I went to "Customs domain" tab under "alicyberblog" Web App in order to define this security key as a legit "SNI SSL" encryption. 
<br/ >-
-<br/ >
<img src="https://i.imgur.com/WQJAnVE.jpeg" height="80%" width="80%" />
<br/ >-
-<br/ >
📜✒️note: since this certificate is not from the trusted source as SSL does, the browser would warn upon receiving "http request" from visitors.
<br/ >
😊But to take it seriosly and igonre the warning. It was just a schoolwork, breathtaking though.
<br/ >

<img src="https://i.imgur.com/CfmTzYc.jpeg" height="80%" width="80%" />

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
