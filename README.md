# Jenkins-Project
### Complete Jenkins CI/CD Project

In this project i will be deploying a node.js application on EC2 instance and i will create a CI/CD pipeline using Jenkins.

**Tools I will be using in the project:**

1. AWS-EC2
2. GitHub
3. Docker
4. Jenkins

****Step 1: Create EC2 instance and ssh into it****
  * Go to AWS management console
  * Create a Ubuntu Ec2 instance with port 8080 and 8000 open in its SG.

****Step 2: Install Jenkins on the server****
  * Use the below commands to install jenkins on the instance that we created
  * Install Java latest version
```
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
openjdk version "17.0.8" 2023-07-18
OpenJDK Runtime Environment (build 17.0.8+7-Debian-1deb12u1)
OpenJDK 64-Bit Server VM (build 17.0.8+7-Debian-1deb12u1, mixed mode, sharing)
```
  * Install Jenkins
```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
  * Go to https://public-ip:8080
  * Paste the admin password
  * Set up your credentials.

****Step 3: Public key to github****

Before starting with the Jenkins configurations we need to add the public key so that we can create a bridge between jenkins and Github for accessing the source code from the Github repo.
  * Go to your EC2 instance and type command
```
ssh-keygen
```
  * It will create 2 keys. CD to .ssh and check the keys.
  * We need to add the Public key to Github so that we can create the bridge.
    
    Go to Github -> setting -> SSH and GPG key ->Add new key -> do cat id_rsa_pub in the EC2instance and copy the key -> give a name to the key -> paste the copied key.

****Step 4: Create project in Jenkins****
 * Create a item in jenkins -> Create a FreeStyle Project -> Add the URL of the repo
 * Now we will add the credentials so that jenkins can access the code from the Github.

   In Source Core Management-> Go to Add credentials
   
   Kind = ssh username with private key
   
   id = project
   
   username = ubuntu or root ( if you are running jenkins as root user )
   
   Then enter the private key of instance
   
   Click on Add -> save it

****Step 5: Build Job****

Now we are all ready to Build the job now. 
 * Click on Build now.
 * Go to instance and check if the repo is cloned there.
 * Now in instance install neccessary dependencies
```
sudo apt install nodejs -y
sudo apt install npm -y
sudo npm install
```
 * Run the application
```
node app.js
```
 * By default  application will ge hosted in 8000 port.
 * To view it visit https://Public-ip:8000

****Step 6: Containerizing the application****

Now we will dockerize the application so that it can be accessed anywhere by anyone.
 * Install Docker in instance
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
docker --version
```
 * Create your Dockerfile
```
FROM node:12.2.0-alpine
WORKDIR app
COPY . .
RUN npm install
EXPOSE 8000
CMD ["node","app.js"]
```
 * Build the image using Dockerfile
```
docker build -t "sanath-image" .
```
 * You can check the image list using
```
docker images
```
 * Create container form image running in 8000 port [ Remember to Kill the existing container.]
```
docker run -d -p 8000:8000 sanath-image
```
 * Check the running instance
```
docker ps
```
 * App is successfully hosted in port 8000
 * Visit https://pubic-ip:8000

****Step 7: Automation****
 * Kill the existing container.
 * Go to build stepts in jenkins
 * Add execute shell and add following commands
```
docker build -t "sanath-image" .
docker run -d -p 8000:8000 sanath-image
```
 * Build again
 * Thats the automation

   Now all the processes that we were doing manually will be performed automatically as we did the automation.

****Step 8: Webhook Configure****
 * Kill the existing container.
 * Jenkins -> Manage Jenkins -> Manage Plugins -> Install github integration plugin
 * Go to repository settings -> webhook -> Add webhook -> Payload URL add jenkins URL here -> (http://13.231.214.11:8080/github-webhook/)
 * Content type -> application.json
 * Add webhook.
 * Web hook added Successfully
 * Go to jenkins -> configure -> tick GitHub hook trigger for GITScm polling

 Now just update something in the repo and your job will be triggered automatically.

 *That's a wrap*

**Note : For any queries or issues, please open an issue in the repository.**
