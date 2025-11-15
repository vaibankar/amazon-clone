# Amazon-clone Deploy on container

### Step 1: Configure AWS in VS Code or CMD or instance

Navigate to either VS Code or CMD based on your preference.
```
aws configure
```
Provide your Aws Access key and Secret Access key

<img width="1103" height="252" alt="image" src="https://github.com/user-attachments/assets/6392a652-9583-4935-aad7-e3d45e237774" />

---
### Step 2: Implement Terraform Configuration for Jenkins and SonarQube Provisioning

<img width="1089" height="393" alt="image" src="https://github.com/user-attachments/assets/d363ad21-bf71-4ae1-ab6a-64e2002efe96" />

---

### Terraform commands to provision

```
terraform init
terraform validate
terraform plan
terraform apply
```
---

<img width="988" height="521" alt="image" src="https://github.com/user-attachments/assets/807ce43c-cb6b-42b1-9c25-77ba0efb2e02" />

---
<img width="742" height="255" alt="image" src="https://github.com/user-attachments/assets/cb17f1b2-b1c7-4e58-8020-1b1b922345c2" />

---
<img width="928" height="588" alt="image" src="https://github.com/user-attachments/assets/b6e55e31-03bf-4196-80f9-f3d3e70aac46" />

---

### output

```
<instance-ip:8080> #jenkins
```

---
<img width="1073" height="601" alt="image" src="https://github.com/user-attachments/assets/858c672e-9aa0-45e7-bf65-86f60181b165" />

---

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Unlock Jenkins by utilizing an administrative password and proceed to install the recommended plugins.

---
<img width="1048" height="553" alt="image" src="https://github.com/user-attachments/assets/e35c53e3-9bc4-4086-b1f9-120da27a5c34" />

---
Now, Jenkins will be installed, and it will proceed to install all the required libraries.

<img width="902" height="537" alt="image" src="https://github.com/user-attachments/assets/70022711-555a-4121-815f-ae639bd50aca" />

---
Prompt the user to click on ‘Save and Continue’ on the Jenkins Getting Started Screen.

Retrieve your public key once more and insert it into a fresh browser tab.

```
<instance-public-ip:9000>
```
---

Our SonarQube instance is now operational and running smoothly.

<img width="1048" height="508" alt="image" src="https://github.com/user-attachments/assets/33454152-8c3a-489b-b1bb-646ee43cbe0b" />

---

Please provide your username and password, then click on the ‘Login’ button. If needed, you can also use the ‘Change Password’ option after logging in.

```
username admin
password admin
```
---

New Password Update: Access the Sonar Dashboard with this latest password.

---

### Check trivy version

```
trivy --version
```

---

### Step 3 — Set up Essential Plugins: JDK, Sonarqube Scanner, NodeJs, and OWASP Dependency Check

4A - Plugin Installation Process
Navigate to Manage Jenkins → Plugins → Available Plugins →

Proceed with the installation of the following plugins:

1. Eclipse Temurin Installer (Install without restarting)

2. SonarQube Scanner (Install without restarting)

3. NodeJs Plugin (Install without restarting)

---

<img width="1109" height="486" alt="image" src="https://github.com/user-attachments/assets/3da01ff1-fc67-4eb6-b9b7-09bbe4280fb4" />

---

4B — Set Up Java and Node.js in Global Tool Configuration
Navigate to Manage Jenkins → Tools → Install JDK 17 and Node.js 16 → Press Apply and Save to confirm changes.

<img width="1144" height="558" alt="image" src="https://github.com/user-attachments/assets/af50d8cc-c87a-43c5-a9c8-702bdde3fb16" />

---

<img width="1138" height="556" alt="image" src="https://github.com/user-attachments/assets/06783849-645b-453c-98d7-6572a0908751" />

---

### Step 4 — Setting Up Sonar Server Configuration in Jenkins

Retrieve the Public IP Address of your EC2 Instance. As Sonarqube operates on Port 9000, access it using <Public IP>:9000. Navigate to your Sonarqube Server and follow these steps:

1. Click on “Administration” in the top menu.
2. Navigate to “Security” and click on “Users.”
3. Inside the “Users” section, select “Tokens.”
4. Click on “Update Token” to modify or create a new token.
5. Provide a meaningful name for the token.
6. Click on “Generate Token” to create the token.

---
Navigate to the Jenkins Dashboard → Access “Manage Jenkins” → Choose “Credentials” → Select “Add Secret Text.” The configuration should resemble the following:

<img width="1113" height="517" alt="image" src="https://github.com/user-attachments/assets/f15c013f-02b4-44ff-a3fa-91e4159c445f" />

You will this page once you click on create

Now, navigate to the Dashboard, then proceed to Manage Jenkins and select System. Add the necessary configurations as depicted in the image below.

<img width="1092" height="562" alt="image" src="https://github.com/user-attachments/assets/75eff48c-a35a-4cb1-87ed-4ea4f3fec171" />

Click ‘Apply’ and ‘Save’ to finalize your changes.

Utilize the ‘Configure System’ option within Jenkins to customize various server settings.

Employ the ‘Global Tool Configuration’ to fine-tune settings for various tools installed through plugins.

Specifically, we are incorporating a Sonar Scanner as part of the installed tools.

---

<img width="1141" height="553" alt="image" src="https://github.com/user-attachments/assets/d929e450-1948-413c-b88c-b2cae7c0b3c2" />

---

Incorporate a quality gate into the Sonarqube Dashboard, and additionally, navigate to Administration -> Configuration -> Webhooks.

<img width="1253" height="216" alt="image" src="https://github.com/user-attachments/assets/a2736674-a37f-43ac-8149-0ee95d212443" />

Click on Create

---

<img width="1291" height="285" alt="image" src="https://github.com/user-attachments/assets/973db4d1-956f-4651-a25f-982d0443eb10" />

---

Add details

```
#in url section of quality gate
<http://jenkins-public-ip:8080>/sonarqube-webhook/
```
---

<img width="1178" height="533" alt="image" src="https://github.com/user-attachments/assets/916c8e95-8e42-4f73-9e49-c1f0da264776" />

---

Let’s navigate to our Pipeline and incorporate the script into our Pipeline Script.

go to your pipeline.txt file  copy all the pipeline and past jenkins pipeline and build now

<img width="1165" height="438" alt="image" src="https://github.com/user-attachments/assets/71d1cc55-2a39-4800-8206-f398b1c04eeb" />

---

To access the report, navigate to the Sonarqube Server and locate the “Projects” section.

<img width="1242" height="466" alt="image" src="https://github.com/user-attachments/assets/50e8dc52-cf75-4273-9c22-f62436136d18" />

The report has been successfully generated, and the status indicates a successful pass. It has scanned a total of 1,000 lines. For a more comprehensive view of the findings, you are encouraged to access the detailed report by navigating to the ‘Issues’ section.

---

### Step 5: Build and push your Docker image for seamless deployment.

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

<img width="1131" height="539" alt="image" src="https://github.com/user-attachments/assets/fac6da13-c1e2-4e14-8414-1af7246a617f" />

---

Now, navigate to Dashboard → Access Jenkins → Configure → Utilities →

<img width="1142" height="555" alt="image" src="https://github.com/user-attachments/assets/8eda037e-38d8-4faa-ba53-ee65555000e6" />

---

Include DockerHub credentials in the Global Credentials section.

<img width="1172" height="563" alt="image" src="https://github.com/user-attachments/assets/5cf27e6d-450d-4738-8b40-f08d4b1a1694" />

---

Integrate the following stage into the Pipeline Script.

```
stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build -t amazon-clone ."
                       sh "docker tag amazon-clone <your-dockerhub-name/reponame:tagname> "
                       sh "docker push <your-dockerhub-name/reponame:tagname> "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image <your-dockerhub-name/reponame:tagname> > trivyimage.txt" 
            }
        }
```

---

When accessing Dockerhub, a new image is automatically generated upon logging in.

Execute the container to verify the successful launch of the game by incorporating the following stage:

```
stage('Deploy to container'){
            steps{
                sh 'docker run -d --name amazon-clone -p 3000:3000 <your-dockerhub-name/reponame:tagname>'
            }
        }
```

---

### stage view

<img width="1332" height="493" alt="image" src="https://github.com/user-attachments/assets/685d6c3d-07c7-4ac7-834d-c9262f55cfd9" />

---

Access the output by navigating to the following address in your web browser: Jenkins-public-ip:3000

<img width="1330" height="621" alt="image" src="https://github.com/user-attachments/assets/1767c296-a3c6-43dc-8497-673c0508f5c5" />

---

Let’s initiate a comprehensive cleanup.

Navigate to VS Code’s PowerShell command line and execute the following command, or alternatively, access the directory where you have provisioned the EC2 instance.

```
terraform destroy --auto-approve
```

---











































