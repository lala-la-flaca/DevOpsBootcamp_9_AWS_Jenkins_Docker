# <img src="https://github.com/user-attachments/assets/56192d5e-c2a7-4584-8b58-d6c8951e2ae4" width="250" /> CD with Jenkins: Deploying Web Application to EC2 with Docker

## Description

This demo project is part of **Module 9**: **AWS Services** from **Nana DevOps Bootcamp**. This project automates the deployment of a Java Maven application from a Jenkins Pipeline to an AWS EC2 instance using Docker. It extends an existing CI pipeline by adding a deployment step to push the latest Docker image to the remote EC2 instance. <br />


## 🚀 Technologies Used

- **Docker**: Docker for containerization.
- **AWS EC2**: Cloud provider for hosting WebApp.
- **Linux**: Ubuntu is used for server configuration and management.
- **DockerHub**: Private docker repository.
- **Git**: Version Control System.
- **GitLab**: Git Repository.
- **Java & Maven**: Nana's application and build tools.
  

## 🎯 Features

- **Automated Deployment**: Deploys application updates directly from Jenkins to EC2.
- **Secure SSH Access**: Uses Jenkins credentials to securely connect to the EC2 instance.
- **Dockerized Application**: Builds and runs the application in a Docker container.
- **Pipeline Integration**: Extends the CI process to include deployment.
- **Security Group Configuration**: Ensures proper access to the deployed application.


## 🏗 Project Architecture

<img src=""/>

## 📝  Prerequisites
- AWS account with permissions to launch EC2.
- Jenkins server running.
- GitLab repository including the application source code.
- Docker Installed from the previous demo.
- Private key PEM file for SSS with EC2 from the previous demo.

## ⚙️ Project Configuration

### Creating a Multibranch pipeline & Adding SSH credentials
1. Create a new Multibranch pipeline.
2. Name the new multibranch pipeline aws-multibranch-pipeline.
3. Configure the Branch source: add the git repository (GitLab), add the credentials to access gitlab, add a behavior to filter by name the branches an use the .* regular expression.
4. Click on the Credentials panel on the left.
5. Click on the aws multibranch under the store scoped to aws-multibranch-pipeline section
6. Click on Add credentials.
7. On the new credentials section in the kind field select SSH username with private key.
8. Add the ID for the credentials and username ec2-user.
9. Enter a key directly and copy and paste the private key from the PEM file from the previous demo.

### Installing SSHAgent Plugin
1. Navigate to the Jenkins server.
2. Click on the Dashboard and go to manage Jenkins.
3. Select Plugin and Install the SSHAgent plugin.

### Verifying EC2
1. Go to the command line and SSH to the EC2.
2. Stop all previous containers.
3. Ensure that the EC2 has no images.
4. Login to Docker using the docker login command.
   
### Deploying the WebApp
1. Go to the AWS multibranch pipeline.
2. Select pipeline syntax on the left.
3. On the overview section select sshagent: SSH agent
4. Add the ec2-user.
5. Click on Generate Pipeline Script.
6. Select the script.
7. Go to the java-maven application and open the Jenkinsfile.
8. Add a deployment step and copy the script.
9. Modify the script by adding the SSH command to access EC2 and the docker run command.
10. Commit changes to the repository.
11. Check EC2 for new images.
12. Check the current container.

### Modyfing Inboud rules to Security group.
1. Navigate to the EC2 instance from the WebUI.
2. Click on Security Groups and edit the inbound rules to allow SSH access from Jenkins server and public access to the WebApp.
3. Check WebApplication using the public IP of the EC2 and the application port.
    

