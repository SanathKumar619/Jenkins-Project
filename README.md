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
