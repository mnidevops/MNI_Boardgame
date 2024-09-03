# BoardgameListingWebApp

## Description

**Board Game Database Full-Stack Web Application.**
This web application displays lists of board games and their reviews. While anyone can view the board game lists and reviews, they are required to log in to add/ edit the board games and their reviews. The 'users' have the authority to add board games to the list and add reviews, and the 'managers' have the authority to edit/ delete the reviews on top of the authorities of users.  

## Technologies

- Java
- Spring Boot
- Amazon Web Services(AWS) EC2
- Thymeleaf
- Thymeleaf Fragments
- HTML5
- CSS
- JavaScript
- Spring MVC
- JDBC
- H2 Database Engine (In-memory)
- JUnit test framework
- Spring Security
- Twitter Bootstrap
- Maven

## Features

- Full-Stack Application
- UI components created with Thymeleaf and styled with Twitter Bootstrap
- Authentication and authorization using Spring Security
  - Authentication by allowing the users to authenticate with a username and password
  - Authorization by granting different permissions based on the roles (non-members, users, and managers)
- Different roles (non-members, users, and managers) with varying levels of permissions
  - Non-members only can see the boardgame lists and reviews
  - Users can add board games and write reviews
  - Managers can edit and delete the reviews
- Deployed the application on AWS EC2
- JUnit test framework for unit testing
- Spring MVC best practices to segregate views, controllers, and database packages
- JDBC for database connectivity and interaction
- CRUD (Create, Read, Update, Delete) operations for managing data in the database
- Schema.sql file to customize the schema and input initial data
- Thymeleaf Fragments to reduce redundancy of repeating HTML elements (head, footer, navigation)

## How to Run

1. Clone the repository
2. Open the project in your IDE of choice
3. Run the application
4. To use initial user data, use the following credentials.
  - username: bugs    |     password: bunny (user role)
  - username: daffy   |     password: duck  (manager role)
5. You can also sign up as a new user and customize your role to play with the application! 😊


-------------------------------------------------------------------------------------------------------------------

# Infrastructure requirements

## End-to-end CICD deployment of the application.

Phase 1:
• Network setup (Private, Secure, Isolated network)
• Kubernetes cluster setup (Deploy application, scan cluster for vulnerabilities)
• Create instances (SonarQube, Nexus, Jenkins, Monitoring tools)
	
Phase 2:
• Create GIT repo (private)
• Push our source code to this repository
• Check if the code is visible in the repository

Phase 3:
• Create a CICD pipeline
• Ensure best practices
• Ensure all security measures
• Deployment of application on k8s cluster
• Mail notification (Success/Failure)
	
### EC2 Instances Required:

	1. Ubuntu t2.medium: (4 instances)
	• 2 K8s cluster (1 master and 1 slave)
	• 1 SonarQube server
	• 1 Nexus server

	2. Ubuntu t2.large: 
	• 1 Jenkins server.

### Security Group In-bound rules
![image](https://github.com/user-attachments/assets/be36df47-df90-4be5-a627-21721bfdfb17)

### Ports used:
	• 30000 – 32767: Used for deploying our main application on the k8s cluster.
	• 465: To be used to send email notifications from our Jenkins server to Gmail.
	• 6443: Will be required at the time of setting up the Kubernetes cluster.
	• 22: To access the EC2 instances we use SSH port 22.
	• 443: For HTTPS.
	• 80: For HTTP.
	• 3000 – 10000: Most of the application gets deployed in this range.
	• 25: This SMTP port is used for sending email notifications. We will not use it.


-------------------------------------------------------------------------------------------------------------------

# Installation Process

## Jenkins Installation Steps:

#### 1. Switch to root user:
     sudo su

#### 2. Create a jenk.sh file with the below script and execute it.
    #!/bin/bash

    # Install OpenJDK 17 JRE Headless
    sudo apt install openjdk-17-jre-headless -y

    # Download Jenkins GPG key
    sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    
    # Add Jenkins repository to package manager sources
    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
    
    # Update package manager repositories
    sudo apt-get update
    
    # Install Jenkins
    sudo apt-get install jenkins -y

#### 3. Install docker on Jenkins Server for future use:

#### Create a dock.sh file with the below script and execute it.
      #!/bin/bash

      # Update package manager repositories
      sudo apt-get update
    
      # Install necessary dependencies
      sudo apt-get install -y ca-certificates curl
    
      # Create directory for Docker GPG key
      sudo install -m 0755 -d /etc/apt/keyrings
    
      # Download Docker's GPG key
      sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    
      # Ensure proper permissions for the key
      sudo chmod a+r /etc/apt/keyrings/docker.asc
    
      # Add Docker repository to Apt sources
      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    
      # Update package manager repositories
      sudo apt-get update
    
      sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin 
    
    
## Setup K8-Cluster using kubeadm [K8 Version-->1.28.1]
    # 1. Update System Packages [On Master & Worker Node]
      sudo apt-get update
    
    
    # 2. Install Docker [On Master & Worker Node]
      sudo apt install docker.io -y
      sudo chmod 666 /var/run/docker.sock
    
    
    # 3. Install Required Dependencies for Kubernetes[On Master & Worker Node]
      sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
      sudo mkdir -p -m 755 /etc/apt/keyrings
    
    
    # 4  Add Kubernetes Repository and GPG Key[On Master & Worker Node]
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee   /etc/apt/sources.list.d/kubernetes.list
    
    
    # 5. Update Package List[On Master & Worker Node]
      sudo apt update
    
    
    # 6. Install Kubernetes Components[On Master & Worker Node]
      sudo apt install -y kubeadm=1.28.1-1.1 kubelet=1.28.1-1.1 kubectl=1.28.1-1.1
    
    
    # 7. Initialize Kubernetes Master Node [On Master Node]
      sudo kubeadm init --pod-network-cidr=10.244.0.0/16
    
    
    # 8. Configure Kubernetes Cluster [On Master Node]
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config
    
    
    # 9. Deploy Networking Solution (Calico) [On Master Node]
      kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
    
    
    # 10. Deploy Ingress Controller (NGINX) [On Master Node]
      kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml

    

# Configuration of Tools

## Jenkins Configuration:

### Jenkins Plugins required:
    1. Eclipse Temurin Installer (JDK)
  	2. Config File Provider
  	3. Pipeline Maven Integration
  	4. Maven Integration
  	5. SonarQube Scanner
  	6. Docker
  	7. Docker Pipeline
  	8. Kubernetes
  	9. Kubernetes CLI
  	10. Kubernetes Client API
  	11. Kubernetes Credentials
  	12. Prometheus Metrics

### Tools configuration in Jenkins:

#### 1. JDK:

![image](https://github.com/user-attachments/assets/04506aa0-c81b-4472-ace3-e2bd80505ab8)\


#### 2. SonarQube Scanner:

![image](https://github.com/user-attachments/assets/d4daa2aa-8c7f-40bf-9170-eaf4198f5b9e)


#### 3. Maven Installation:

![image](https://github.com/user-attachments/assets/b51c43d7-8306-4d53-82cd-250704a87c4e)


#### 4. Docker Configuration:

![image](https://github.com/user-attachments/assets/ba8c9fa8-eba9-4538-9f75-977dfbda6790)


#### 5. Email Notification Configuration:

![image](https://github.com/user-attachments/assets/45f11866-ebea-468f-b5cf-b7178c8cee59)

![image](https://github.com/user-attachments/assets/ba94c681-48c9-413a-b64f-bc6b53066b4b)


# Jenkins Pipeline

      pipeline
    {
        agent any
        
        tools
        {
            jdk 'jdk17'
            maven 'maven399'
            
        }
        
        environment
        {
            SCANNER_HOME = tool "sonar-scanner" 
        }
        
        stages
        {
            stage ('Code Checkout')
            {
                steps
                {
                   git branch: 'main', credentialsId: 'git-creds', url: 'https://github.com/mnidevops/MNI_Boardgame.git' 
                }
            }
            
            stage ('Code Compilation')
            {
                steps
                {
                   sh "mvn compile"
                }
            }
            
            stage ('Testing')
            {
                steps
                {
                   sh "mvn test"
                }
            }
            
            stage ('Filesystem Scan Report')
            {
                steps
                {
                   sh "trivy fs --format table -o trivy-fs-report.html ."
                }
            }
            
            stage ('SonarQube Analysis')
            {
                steps
                {
                   withSonarQubeEnv('sonar') 
                   {
                        sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=MNI_Boardgame -Dsonar.projectKey=MNI_Boardgame \
                        -Dsonar.java.binaries=.'''
                   }
                }
            }
            
          /*  stage ('Quality Gate Check')
            {
                steps
                {
                   script
                   {
                       waitForQualityGate abortPipeline: false, credentialsId: "sonar-cred"
                   }
                }
            }
        */    
            stage ('Build the application')
            {
                steps
                {
                    sh "mvn package"
                }
                    
            }
            
            stage ('Publish Artifact to Nexus')
            {
                steps
                {
                    withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven399', mavenSettingsConfig: '', traceability: true) {
                        sh "mvn deploy"
                    }
                }
                    
            }
            
            stage ('Build and Tag Docker Image')
            {
                steps
                {
                    script 
                    {
                        withDockerRegistry(credentialsId: 'docker-creds', toolName: 'docker') 
                        {
                            sh "docker build -t mnidevops/mni_boardgame ."
                        }    
                    }
                }
            }
            
            stage ('Docker Image Scan Report')
            {
                steps
                {
                   sh "trivy image --format table -o trivy-image-report.html mnidevops/mni_boardgame.latest"
                }
            }
            
            stage ('Push Docker Image')
            {
                steps
                {
                    script 
                    {
                        withDockerRegistry(credentialsId: 'docker-creds', toolName: 'docker') 
                        {
                            sh "docker push mnidevops/mni_boardgame.latest"
                        }    
                    }
                }
            }
            
            stage ('Deploy To Kubernetes')
            {
                steps
                {
                   withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8s-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.4.174:6443') 
                   {
                        sh "kubectl apply -f deployment-service.yaml"  
                   }
                }
            }
            
            stage ('Verify Deployment')
            {
                steps
                {
                   withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8s-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.4.174:6443') 
                   {
                        sh "kubectl get pods -n webapps"  
                        sh "kubectl get svc -n webapps"
                   }
                }
            }
    
        }
        
        post {
        always {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
                def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'
    
                def body = """
                    <html>
                    <body>
                    <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                    <h2>${jobName} - Build ${buildNumber}</h2>
                    <div style="background-color: ${bannerColor}; padding: 10px;">
                    <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                    </div>
                    <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                    </div>
                    </body>
                    </html>
                """
    
                emailext (
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                    body: body,
                    to: 'mnidevops@gmail.com,djguru121@gmail.com',
                    from: 'mnidevops@gmail.com',
                    replyTo: 'mnidevops@gmail.com',
                    mimeType: 'text/html',
                    attachmentsPattern: 'trivy-image-report.html'
                )
            }
        }
    }    
    }



# Screens

## Jenkins Pipeline:

![image](https://github.com/user-attachments/assets/a231a5df-560d-452a-b848-84233df9fffe)


## Jenkins Workspace:

![image](https://github.com/user-attachments/assets/ffe408f5-4fd3-498c-afc6-d6cd518cb5aa)


## SonarQube Report:

![image](https://github.com/user-attachments/assets/a7aa4d1c-5242-4047-8155-9b70ebe81380)


## Nexus Artefact releases

![image](https://github.com/user-attachments/assets/437e7193-94ea-4f2f-807e-81d6562a9037)


## Docker Hub Registry:

![image](https://github.com/user-attachments/assets/03cec7cb-1f3f-417b-ad4f-0cc95482a203)


## Application Screens:

![image](https://github.com/user-attachments/assets/fdfc1691-5d9c-4474-84f3-3fa95a72897c)


![image](https://github.com/user-attachments/assets/86cc40c8-0fcf-4db5-aff2-047ef4d47e98)


![image](https://github.com/user-attachments/assets/f46e1e83-3a46-4a97-8d01-0559b97dd1f3)


![image](https://github.com/user-attachments/assets/6cc2dbd5-1bc2-4bda-831e-ed9521a733df)


![image](https://github.com/user-attachments/assets/4187c8b4-0eb3-46a8-ab97-5fa0d17a0b35)


![image](https://github.com/user-attachments/assets/e4d656b9-faa7-4e36-944d-d9e51dddbf39)








































