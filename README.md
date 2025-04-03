# <img src="https://github.com/user-attachments/assets/56192d5e-c2a7-4584-8b58-d6c8951e2ae4" width="250" /> CD with Jenkins: Deploying Web Application to EC2 with Docker

## Description

This demo project is part of **Module 9**: **AWS Services** from **Nana DevOps Bootcamp**. This project automates the deployment of a Java Maven application from a Jenkins Pipeline to an AWS EC2 instance using Docker. It extends an existing CI pipeline by adding a deployment step to push the latest Docker image to the remote EC2 instance. <br />


## 🚀 Technologies Used

- <b>Docker: Docker for containerization.</b>
- <b>AWS EC2: Cloud provider for hosting WebApp.</b>
- <b>Linux: Ubuntu for Server configuration and management.</b>
- <b>DockerHub: Private docker repository.</b>
- <b>Git: Version Control System<b/>
- <b>GitLab: Git Repository<b/>
- <b>Java & Maven: Nana's application and build tools<b/>
  

## 🎯 Features

- Automated Deployment: Deploys application updates directly from Jenkins to EC2.
- Secure SSH Access: Uses Jenkins credentials to securely connect to the EC2 instance.
- Dockerized Application: Builds and runs the application in a Docker container.
- Pipeline Integration: Extends the CI process to include deployment.
- Security Group Configuration: Ensures proper access to the deployed application.


## 🏗 Project Architecture

<img src=""/>
