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
                      //code
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
    ```bash
      git add .
      git commit -m "Jenkins file with deployment step"
      git push
    ```
12. Navigate to the Jenkins server and check the status of the pipeline.

    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/15%20Build%20Pipeline%20Manually%20from%20jenkins.png" width=800 />
   
13. Verify that the new Docker images are available on the EC2 instance.

    ```bash
      docker images
     ```
   
14. Check the status of the running container.

    ```bash
      docker ps
    ```

    <img src= "https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/15%20docker%20images%20and%20container%20up%20on%20ec2.png" width=800 />


### Modifying Security Group Inbound Rules.
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

 

### Deploying Java-Maven-App using a shared library
1. Edit the Jenkinsfile to reference the shared library used in the previous demo.
   <details><summary><strong> 💡 Using the Shared Library  </strong></summary>
      Because the shared library was originally created using the java-maven-app directory structure, a new branch is required to support the AWS multibranch pipeline, which does not include this directory and places the pom.xml file in the root directory. To apply this difference, a new branch named aws-multibranch was created in the shared library. This section uses the aws-multibranch branch of the shared library to ensure compatibility with the AWS multibranch pipeline.
  </details>
  
  ```bash
         #!/usr/bin/env groovy
          
          //Defining the library only for specific project
          
          library identifier: 'jenkins-shared-library@aws-multibranch', retriever: modernSCM(
              [$class: 'GitSCMSource',
               remote: 'https://github.com/lala-la-flaca/DevOpsBootcamp_8_Jenkins_SharedLibrary.git',
               credentialsId: 'github-credentials2'])
          
          
          
          pipeline {
              agent any
              tools{
                  maven 'maven-3.9'
              }
          
              environment{
                  IMAGE_NAME = 'lala011/demo-app:jma-4.0'
              }
          
              stages{
          
                   stage("build jar"){
                       steps{
                           script{
                               echo 'building the application'
                               buildJar()
          
                           }
                        }
                  }
          
                   stage("build and Push image") {
                      steps {
                          script {
                              echo "building the docker image..."
                              buildImage(env.IMAGE_NAME)
                              dockerLogin()
                              dockerPush(env.IMAGE_NAME)
          
                          }
                      }
                   }
          
          
                  stage("deploy") {
          
                       steps {
                          script {
                              //gv.deployApp()
                              echo "Deploying docker image to EC2"
                              def dockerCmd = "docker run -p 8080:8080 -d ${IMAGE_NAME}"
                              sshagent(['ec2-server-key']) {
                                  sh "ssh -o StrictHostKeyChecking=no ec2-user@13.59.163.202 ${dockerCmd}"
                              }
          
                          }
                      }
                  }
          
          
              }
          }
  ```


  <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/21%20jenkinsfile%20updated%20with%20shared%20library.png" width=800 />

  <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/22%20jenkinsfile%20using%20sharelibrary.png" width=800 />

2. Commit the changes and build the aws-multibranch-pipeline

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/17%20using%20shared%20library%20to%20ec2.PNG" width=800 />
   
4. Access the EC2 instance using SSH.
5. Verify that the new Docker image and container are running.
   
   ```bash
   docker images
   docker ps
   ```
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/18%20checkig%20EC2%20with%20new%20images%20and%20new%20container.png" width=800 />
   
6. Update the EC2 security group to allow inbound access on the required port.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/19%20editing%20inboud%20urles%20SG.png" width=800 />

7. Access the App from the browser.

   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_9_AWS_Jenkins_Docker/blob/main/Img/20%20java-maven%20app%20runing%20on%20ec2%20ok.png" width=800 />
   
   
 ### Using Docker-Compose
 1.  Install docker-compose on the EC2 instance.

        ```bash
          sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
        ```
    
      <img src="" width=800/>
    
 3.  Stop all the running containers and remove existing docker images.
    
      ```bash
        docker stop
        docker rmi -f 
      ```
      <img src="" width=800/>
    
 5.  Update the Jenkinsfile to execute the docker-compose command and Copy the docker-compose file.yaml file to the EC2 instance.
    
      ```bash
          stage("deploy") {
      
                   steps {
                      script {
                          echo "Deploying docker image to EC2"
                          def dockerComposeCmd = "docker-compose -f docker-compose.yaml up --detach"
                        
                          sshagent(['ec2-server-key']) {
                              sh "scp docker-compose.yaml ec2-user@13.59.163.202:/home/ec2-user"
                              sh "ssh -o StrictHostKeyChecking=no ec2-user@13.59.163.202 ${dockerComposeCmd}"
                          }
      
                      }
                  }
              }
      ```
        <img src="" width=800/>
    
 9.  Save and commit the changes to the repository.

     ```bash
        git add .
        git commit -m "Jenkinsfile with docker-compose"
        git push
      ```
    
 11.  Run the Jenkins Job.

       <img src="" width=800/>

### Extract Linux Commands into a separate shell Script
1. Create a new Shell Script file.
2. Add the docker-compose and echo commands to the shell script.
   
    ```bash
        docker-compose -f docker-compose.yaml up --detach
        echo "success"
    ```
    
3. Update the Jenkins file and modify the variable to call the shell script.
   
    ```bash
      stage("deploy") {
  
               steps {
                  script {
                      echo "Deploying docker image to EC2"
  
                      //calling and passing image parameter to the bash script
                      def shellCmd = "bash ./server-cmds.sh"
  
                      sshagent(['ec2-server-key']) {
                          //copying shell script to EC2
                          sh "scp server-cmds.sh ec2-user@13.59.163.202:/home/ec2-user"
                          
                          //copying docker-compose.yaml file to EC2
                          sh "scp docker-compose.yaml ec2-user@13.59.163.202:/home/ec2-user"

                          //Connecting to EC2 via SSH and running docker-compose using the script
                          sh "ssh -o StrictHostKeyChecking=no ec2-user@13.59.163.202 ${shellCmd}"
                      }
  
                  }
              }
          }
      
    ```
    
5. Save and commit the changes to the repository.

    ```bash
        git add .
        git commit -m "Jenkinsfile with docker-compose"
        git push
    ```
7. Run the Jenkins Job.
   
   
 
