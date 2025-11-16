# Amazon-clone Deploy on Container

## Architecture

<img width="1240" height="640" alt="image" src="https://github.com/user-attachments/assets/1281fbca-4e9e-4d59-92f9-e58d988cbc66" />

---
### Step 1: IAM User Creation

1. Access the AWS Console
2. Locate and select the “Search” field.
3. Navigate IAM Options
4. Click on “Users”
5. Click ‘Create Users’
6. Click the “User name” field.
7. Enter the name you’d like to send warm wishes to or express your thoughts about.
8. Click Next to spread joy and positivity!
9. Access the “Attach policies directly” feature by clicking.
  Check this box with administrator privileges.

<img width="1137" height="550" alt="image" src="https://github.com/user-attachments/assets/e1f15032-914e-4c98-a21b-0b25e02c2ae5" />

10. Click “Next”
11. Click “Create User”
12. Select the recently generated user, in this instance, User01.
13. Open the “Security credentials” tab and click.
14. Generate Access Key
15. Select this radio button using the Command Line Interface (CLI)
16. Click Next
17. Download your data in .csv format

---
### Step 2: Configure AWS in VS Code or CMD or instance

Navigate to either VS Code or CMD based on your preference.
```
aws configure
```
---

Provide your Aws Access key and Secret Access key
<img width="1053" height="255" alt="image" src="https://github.com/user-attachments/assets/c546e145-d886-423f-9b27-b0cd9065e601" />

---
### Step 3: Implement Terraform Configuration for Jenkins and SonarQube Provisioning

## main.tf

```
resource "aws_instance" "web" {
  ami                    = "ami-0f5ee92e2d63afc18"   #change ami id for different region
  instance_type          = "t2.large"
  key_name               = "Mumbai"
  vpc_security_group_ids = [aws_security_group.Jenkins-sg.id]
  user_data              = templatefile("./install.sh", {})

  tags = {
    Name = "Jenkins-sonarqube"
  }

  root_block_device {
    volume_size = 30
  }
}

resource "aws_security_group" "Jenkins-sg" {
  name        = "Jenkins-sg"
  description = "Allow TLS inbound traffic"

  ingress = [
    for port in [22, 80, 443, 8080, 9000, 3000] : {
      description      = "inbound rules"
      from_port        = port
      to_port          = port
      protocol         = "tcp"
      cidr_blocks      = ["0.0.0.0/0"]
      ipv6_cidr_blocks = []
      prefix_list_ids  = []
      security_groups  = []
      self             = false
    }
  ]

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "jenkins-sg"
  }
}
```
---

## provider.tf

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# Configure the AWS Provider
provider "aws" {
  region = "ap-south-1"  #change your region
}
```
---

## install.sh
This command will deploy Jenkins, Docker, Sonarqube, and Trivy.
```
#!/bin/bash
sudo apt update -y
wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
sudo apt update -y
sudo apt install temurin-17-jdk -y
/usr/bin/java --version
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl status jenkins

#install docker
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker ubuntu  
newgrp docker
sudo chmod 777 /var/run/docker.sock
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

#install trivy
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```

<img width="691" height="382" alt="image" src="https://github.com/user-attachments/assets/d6ce126f-7c2e-426c-a33c-7d40f0665ce3" />

---
## Terraform commands to provision

```
terraform init
terraform validate
terraform plan
terraform apply
```
---

<img width="1010" height="524" alt="image" src="https://github.com/user-attachments/assets/af0b2b8a-162d-42a1-95ba-80f55238bc90" />

---

<img width="889" height="254" alt="image" src="https://github.com/user-attachments/assets/85eef424-ad97-45a3-be44-e8ed8288d33e" />

---

<img width="1057" height="592" alt="image" src="https://github.com/user-attachments/assets/dfbf4667-5926-4141-b7fc-deebded97b74" />

---

## output
```
<instance-ip:8080> #jenkins
```
---

<img width="1049" height="581" alt="image" src="https://github.com/user-attachments/assets/09ff7086-2f5f-4c17-b91d-575864d00d4e" />

---

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
---

Unlock Jenkins by utilizing an administrative password and proceed to install the recommended plugins.

Now, Jenkins will be installed, and it will proceed to install all the required libraries.

Prompt the user to click on ‘Save and Continue’ on the Jenkins Getting Started Screen.

<img width="980" height="538" alt="image" src="https://github.com/user-attachments/assets/c737e43d-160b-4303-bcfc-af8df9fbda26" />

---

Retrieve your public key once more and insert it into a fresh browser tab.

```
<instance-public-ip:9000>
```
---

Our SonarQube instance is now operational and running smoothly.

<img width="1013" height="512" alt="image" src="https://github.com/user-attachments/assets/ee364daa-12af-48d1-b2b5-a130cdebdd2c" />

Please provide your username and password, then click on the ‘Login’ button. If needed, you can also use the ‘Change Password’ option after logging in.

```
username admin
password admin
```
---

<img width="853" height="499" alt="image" src="https://github.com/user-attachments/assets/f27ce7d3-8a6f-464a-9670-506632672055" />

New Password Update: Access the Sonar Dashboard with this latest password.

<img width="1131" height="512" alt="image" src="https://github.com/user-attachments/assets/0004b7b5-4d06-47df-8d3f-712da0b53e6e" />

---

Check trivy version
```
trivy --version
```
---

<img width="705" height="124" alt="image" src="https://github.com/user-attachments/assets/b8adb2bf-2203-40b8-95c5-0c634a053a26" />

---

### Step 4 — Set up Essential Plugins: JDK, Sonarqube Scanner, NodeJs, and OWASP Dependency Check

### 4A — Plugin Installation Process

Navigate to Manage Jenkins → Plugins → Available Plugins →

Proceed with the installation of the following plugins:

1. Eclipse Temurin Installer (Install without restarting)

2. SonarQube Scanner (Install without restarting)

3. NodeJs Plugin (Install without restarting)

<img width="981" height="551" alt="image" src="https://github.com/user-attachments/assets/884f877b-4855-47ac-9e5d-93863bd89879" />

---

### 4B — Set Up Java and Node.js in Global Tool Configuration

Navigate to Manage Jenkins → Tools → Install JDK 17 and Node.js 16 → Press Apply and Save to confirm changes.

<img width="1088" height="561" alt="image" src="https://github.com/user-attachments/assets/da6d41c5-2dc4-40dc-8dfc-bddc766bd5b8" />

---

<img width="1135" height="557" alt="image" src="https://github.com/user-attachments/assets/140b5e16-5858-4c91-a658-2044cfa849f5" />

---

### Step 5 — Setting Up Sonar Server Configuration in Jenkins

Retrieve the Public IP Address of your EC2 Instance. As Sonarqube operates on Port 9000, access it using <Public IP>:9000. Navigate to your Sonarqube Server and follow these steps:

1. Click on “Administration” in the top menu.
2. Navigate to “Security” and click on “Users.”
3. Inside the “Users” section, select “Tokens.”
4. Click on “Update Token” to modify or create a new token.
5. Provide a meaningful name for the token.
6. Click on “Generate Token” to create the token.

---
Navigate to the Jenkins Dashboard → Access “Manage Jenkins” → Choose “Credentials” → Select “Add Secret Text.” The configuration should resemble the following:

<img width="995" height="556" alt="image" src="https://github.com/user-attachments/assets/b9d2dd7f-1166-44e4-baf6-345cda4f2c7a" />

---

You will this page once you click on create

<img width="1301" height="312" alt="image" src="https://github.com/user-attachments/assets/814cbd84-eff9-4fa2-a269-a163edde4105" />

---

Now, navigate to the Dashboard, then proceed to Manage Jenkins and select System. Add the necessary configurations as depicted in the image below.

<img width="1151" height="556" alt="image" src="https://github.com/user-attachments/assets/f9c245d2-340b-412c-bdea-7e52598b44d7" />

---

Click ‘Apply’ and ‘Save’ to finalize your changes.

Utilize the ‘Configure System’ option within Jenkins to customize various server settings.

Employ the ‘Global Tool Configuration’ to fine-tune settings for various tools installed through plugins.

Specifically, we are incorporating a Sonar Scanner as part of the installed tools.

<img width="1158" height="563" alt="image" src="https://github.com/user-attachments/assets/e15106fb-3f24-42ea-80cd-d6c6f2092919" />

---

Incorporate a quality gate into the Sonarqube Dashboard, and additionally, navigate to Administration -> Configuration -> Webhooks.


<img width="1231" height="468" alt="image" src="https://github.com/user-attachments/assets/4e69be2f-7684-4fb6-b4ac-60fa1dc9a2ad" />

Click on Create

<img width="1291" height="396" alt="image" src="https://github.com/user-attachments/assets/2b640dd5-7c87-4c9a-9f11-9454ebdc7819" />

Add details

```
#in url section of quality gate
<http://jenkins-public-ip:8080>/sonarqube-webhook/
```

<img width="1168" height="536" alt="image" src="https://github.com/user-attachments/assets/d6188295-88a1-4c7b-8f37-9a7139b62ccc" />

Let’s navigate to our Pipeline and incorporate the script into our Pipeline Script.

```
pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                <your-github-repo>
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Amazon \
                    -Dsonar.projectKey=Amazon '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
                }
            } 
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}
```
---

Click the ‘Build Now’ button, and you’ll be directed to the stage view, presenting a layout similar to this.


<img width="1163" height="469" alt="image" src="https://github.com/user-attachments/assets/b61130e8-81ff-4c14-b087-68e4d7ee6f6b" />

---

To access the report, navigate to the Sonarqube Server and locate the “Projects” section.


<img width="1222" height="417" alt="image" src="https://github.com/user-attachments/assets/c3d639c5-2dff-44cb-8352-7aec6697131f" />

The report has been successfully generated, and the status indicates a successful pass. It has scanned a total of 1,000 lines. For a more comprehensive view of the findings, you are encouraged to access the detailed report by navigating to the ‘Issues’ section.

---

### Step 6 — Integrate OWASP Dependency Check Plugins

Navigate to the Dashboard, then proceed to Manage Jenkins. From there, head to the Plugins section, and locate OWASP Dependency-Check. Click on the plugin, and proceed to install it seamlessly, avoiding the need for a system restart.


<img width="1155" height="508" alt="image" src="https://github.com/user-attachments/assets/f8a55df3-0298-427b-b19e-15143ad67257" />

Initially, we set up the Plugin, and subsequently, we proceeded to configure the Tool.

Navigate to the Dashboard → Access Jenkins → Tools →


<img width="1131" height="555" alt="image" src="https://github.com/user-attachments/assets/13328fc3-3457-4bd3-859f-94b9e2ce317a" />

Select “Apply and Save” to proceed.

Next, navigate to the “Configure” section and access the “Pipeline” settings. Integrate the newly added stage into your existing pipeline and initiate the build process.

```
stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
```
---

The stage perspective will appear as follows,


<img width="1173" height="469" alt="image" src="https://github.com/user-attachments/assets/2abac3af-042e-41cc-84ff-2d2f8e10fcc6" />

---


<img width="1185" height="559" alt="image" src="https://github.com/user-attachments/assets/104238dd-28bb-4966-a3dd-33738cf7055f" />

In the status, you will observe the generation of a graph alongside vulnerabilities.


<img width="1178" height="541" alt="image" src="https://github.com/user-attachments/assets/70f213c9-076c-4a68-8722-372c47ddabdd" />

---

### Step 7: Build and push your Docker image for seamless deployment.

To set up Docker in our system, follow these steps:

1. Open the Dashboard and navigate to “Manage Plugins.”
2. In the “Available plugins” section, search for Docker.
3. Install the following plugins:

- Docker
— Docker Commons
— Docker Pipeline
— Docker API
— docker-build-step

4. Click on the “Install” button for each plugin.
5. Ensure that you install the plugins without restarting the system.

This will enable Docker and its associated components on your system, allowing you to seamlessly integrate Docker into your development environment.


<img width="1194" height="533" alt="image" src="https://github.com/user-attachments/assets/ba3c66fe-d003-43bb-a33b-f08f8b9b3d93" />

Now, navigate to Dashboard → Access Jenkins → Configure → Utilities →


<img width="1129" height="551" alt="image" src="https://github.com/user-attachments/assets/c8edad3e-6b8a-403e-b472-e9563dd56b5f" />

Include DockerHub credentials in the Global Credentials section.

Integrate the following stage into the Pipeline Script.

```
stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build -t amazon ."
                       sh "docker tag amazon <your-dockerhub-username/reponame:tagname> "
                       sh "docker push <your-dockerhub-username/reponame:tagname> "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image <your-dockerhub-username/reponame:tagname> > trivyimage.txt" 
            }
        }
```
---

When accessing Dockerhub, a new image is automatically generated upon logging in.

Execute the container to verify the successful launch of the game by incorporating the following stage:

```
stage('Deploy to container'){
            steps{
                sh 'docker run -d --name amazon -p 3000:3000 <your-dockerhub-username/reponame:tagname>'
            }
        }
```
---

### stage view

<img width="1206" height="530" alt="image" src="https://github.com/user-attachments/assets/cdd2ae81-971b-4a65-925f-d42cae791359" />

Access the output by navigating to the following address in your web browser: Jenkins-public-ip:3000

<img width="1137" height="583" alt="image" src="https://github.com/user-attachments/assets/8a4b2987-0db8-4922-a6b3-32aebe9c23d8" />

---

Let’s initiate a comprehensive cleanup.

Navigate to VS Code’s PowerShell command line and execute the following command, or alternatively, access the directory where you have provisioned the EC2 instance.

```
terraform destroy --auto-approve
```
---

<img width="1155" height="625" alt="image" src="https://github.com/user-attachments/assets/81307bcb-0390-4340-988f-4dabccac39ec" />

---

<img width="721" height="635" alt="image" src="https://github.com/user-attachments/assets/029e5484-5d50-4408-8abe-38c907300046" />

---














































































































































































































































































