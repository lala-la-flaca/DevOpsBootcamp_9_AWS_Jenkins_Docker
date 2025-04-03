# <img src="https://github.com/user-attachments/assets/56192d5e-c2a7-4584-8b58-d6c8951e2ae4" width="250" /> CD with Jenkins: Deploying WebApp to EC2 Using Docker

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

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/3%20Create%20a%20New%20aws%20multibranch%20pipeline.png" width=800 />
   
3. Configure the Branch source:
   * Add the git repository (GitLab).
   * Add the credentials required to access Gitlab.
   * Define a behavior to filter branches by name using the .* regular expression.

  
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/4%20Creating%20multibranch%20pipeline%202.png" width=800 />
   
4. In Jenkins, navigate to the Credentials panel on the left.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/5%20Adding%20aws%20pipeline%20credentials%20scope.png" width=800 />
   
5. Select aws-multibranch under the Store scoped to aws-multibranch-pipeline section.
   
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/5%20Adding%20aws%20pipeline%20credentials%20scope%20glbal.png" width=800 />
   
6. Click Add Credentials.
   
   <img src= "https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/6%20add%20credentials.png" width=800 />
   
7. In the New Credentials section:
   * Set Kind to SSH Username with Private Key.
   * Enter an ID for the credentials.
   * Set Username to ec2-user.
   * Select Enter a key directly and paste the private key from the .pem file used in the previous demo.
     
8. Save the configuration.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/7%20saving%20ssh%20private%20key%20credentials.png" width=800 />
   


### Installing SSHAgent Plugin
1. Navigate to the Jenkins server.
2. On the Dashboard, click Manage Jenkins.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/1%20Jenkins%20%20plugin.png" width=800 />
   
4. Select Plugins and install the SSH Agent plugin.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/2%20Installing%20SSH%20agents%20on%20Jenkins.png" width=800 />
   

### Verifying EC2
1. Open the command line and establish an SSH connection to the EC2 instance, using the PEM file.
   
   ```bash
     ssh -i ~/.ssh/docker-server.pem ec2-user@13.59.163.202
   ```

3. Stop all running containers.
      
   ```bash
     docker ps
     docker stop 695f76cb8e39
   ```

5. Remove any existing Docker images to ensure a clean environment.
      
   ```bash
     docker images
     docker rmi -f 173a605dc5b4
     docker images   
   ```

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/13%20deleting%20old%20images%20from%20ec2.png" width=800 />
   
6. Log in to Docker using the docker login command, as we are using DockerHub we do not need to specify the server. Otherwise, the command must include the URL server
      
   ```bash
     docker login
   ```

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/12%20%20dockerloing%20on%20ec2.png" width=800 />
   
   
### Deploying the WebApp
1. Open the AWS multibranch pipeline in Jenkins.
2. Click Pipeline Syntax on the left panel.
   
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/9%20pipeline%20syntax.png" width=800 />
   
3. In the Overview section, select sshagent: SSH Agent as the step type.
4. Add ec2-user as the SSH credential.
5. Click Generate Pipeline Script to create the script.
6. Copy the generated script.

   ```bash
   sshagent(['ec2-server-key']) {
                      
                    }
   ```
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/10%20generate%20script%20for%20the%20ssh%20agent.png" width=800 />
   
7. Navigate to the java-maven application repository and open the Jenkinsfile.
8. Add a deployment step and paste the copied script.
9. Modify the script to include the SSH command for accessing the EC2 instance and the docker run command.
       
   ```bash
    stage("deploy") {
            steps {
                script {
                    echo "Deploying the application..."
                    def dockerCmd = 'docker run -p 3080:3080 -d lala011/demo-app:web-app-1.0'
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@13.59.163.202 ${dockerCmd}"
                    }
                }
            }
        }               

   ```

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/11%20Modifying%20dockerfile%20to%20ssh%20ec2.png" width=800 />

11. Commit the changes to the repository.

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/15%20Build%20Pipeline%20Manually%20from%20jenkins.png" width=800 />
   
12. Verify that the new Docker images are available on the EC2 instance.

    ```bash
      docker images
     ```
   
13. Check the status of the running container.

    ```bash
      docker ps
    ```

    <img src= "https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/15%20docker%20images%20and%20container%20up%20on%20ec2.png" width=800 />


### Modyfing Inboud rules to Security group.
1. Open the AWS Management Console and navigate to the EC2 service.
2. Select the target EC2 instance from the instance list.
3. Click on the Security Groups associated with the instance.
4. Edit the Inbound Rules:
   * Add a rule to allow SSH access from the Jenkins server’s IP.
   * Add a rule to allow public access to the WebApp on its designated port.
5. Save the changes.

   
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/14%20Editing%20inboud%20security%20group%20to%20allow%20jenkins%20ssh%20ec2.png" width=800 />
   
7. Open a browser and enter the EC2 public IP followed by the application port to verify that the WebApp is accessible.

   [WebApp-Ec2](http://13.59.163.202:3080/)

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/16%20App%20running%20on%20EC2%20from%20multibranch%20pipeline%20manually.png" width=800 />

 

