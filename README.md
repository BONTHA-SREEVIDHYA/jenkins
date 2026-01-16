# jenkins

Pre-requisite for **installing Jenkins **is JDK 
If ubuntu : sudo apt install -y openjdk-17-jdk-headless

vi Jenkins.sh
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```
sudo apt update
sudo apt install jenkins

chmod +x Jenkins.sh 
./Jenkins.sh

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

Can Access the Jenkins UI at <public_ip_ec2>:8080
You can fetch the initialAdminPassword at 
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword 
```

parameters:

Freestyle: This project is parameterized 

Pipeline:

```groovy

pipeline{
  agent any
  parameters {
    choice(name: 'Branch_name', choices:['main','dev','feature1'])
    string(name: 'STRING_PARAM', defaultValue: 'default value', description: 'Enter a string value') 
  }
  stages{
    stage('Git Checkout') {
      steps {
        git branch: "${params.Branch_name}",
        url: 'https://github.com/jaiswaladi246/FullStack-Blog.git'
      }
    }
    stage('Hello'){
      steps {
        echo "Hello World"
      }
    }
  }
```

Sample Jenkinsfile:

```groovy
pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    
    stages {   
        stage('Compile') {
            steps {
            sh 'mvn compile'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
    }
}
```
Authorization -- Matrix Based 
Groups are not there by default--- need to use 3pp's like LDAP
<img width="1734" height="703" alt="image" src="https://github.com/user-attachments/assets/31de7713-5896-49ee-b691-9caf65eff7a1" />

========================================================================
Automatic jenkins job trigger on git push 
we need to set a github webhook trigger for it 
1. Install generic webhook trigger plugin
   <img width="1202" height="450" alt="image" src="https://github.com/user-attachments/assets/1f084899-a752-42bb-a143-4a84b4050b21" />
2. configure the build parameter
   <img width="878" height="361" alt="image" src="https://github.com/user-attachments/assets/965b653e-a5cd-4eaa-bff5-ffc798d6c848" />
   <img width="1246" height="386" alt="image" src="https://github.com/user-attachments/assets/a05570ae-9cb7-4a22-a838-d45c69d5ba41" />
    Add token credential (create a jenkins secret text to uniquely identify the job by github webook trigger.)
   <img width="1456" height="147" alt="image" src="https://github.com/user-attachments/assets/a9011c18-1927-4b27-b24a-e83088a97589" />
   if you only want to run on a specific branch( develop is a branch)
   <img width="1179" height="338" alt="image" src="https://github.com/user-attachments/assets/60693bf9-d147-42f4-bc5d-2f3c1f085ec9" />
   configure the webhook trigger url
   <img width="1425" height="73" alt="image" src="https://github.com/user-attachments/assets/f2a66b84-dafa-4d09-8477-51aacf38b89e" />
3. In github configure the webhook 
   <img width="1627" height="824" alt="image" src="https://github.com/user-attachments/assets/5d5acfdb-2421-4434-ab41-d5f7ebcd01a9" />


for multibranch --- multibranch scan webhook trigger

=====================================================================================
upstream and downstream job
<img width="976" height="79" alt="image" src="https://github.com/user-attachments/assets/590f60de-b46e-4d6e-9eca-63590cc1ddc9" />
<img width="872" height="585" alt="image" src="https://github.com/user-attachments/assets/374aaee9-ce83-4f0c-b4ac-ae729cec0be6" />

if freestyle project -- post build actions -- build other projects
<img width="466" height="490" alt="image" src="https://github.com/user-attachments/assets/aa8d75b0-3f94-469a-8a8b-7f2dd0ccf88a" />

===================================================================================

Deploying application to tomcat webserver

tomcat is opensource webserver
(Can be used for demo to clients) 
Java, Java Servlet pages can be deployed using tomcat

Considered an application: https://github.com/BONTHA-SREEVIDHYA/Tomcat.git
On an ec2 instance, installed java using war package on a different port: 8082 as tomcat by default has 8080 port 
```bash
sudo apt-get update
sudo apt install -y openjdk-17-jdk-headless
wget https://get.jenkins.io/war-stable/latest/jenkins.war
java -jar jenkins.war --httpPort=8082 ( open the port in security group of ec2 )
```
Access the jenkins @ <public-ip > : 8080

In Jenkins GUI: 

Install Maven ( in tools & configure maven3)

Install Stageview & deploy to container plugins 

Install tomcat on your ec2 

Take a duplicate tab as jenkins is actively running in one tab (mobaXterm)
cd /opt
sudo wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.85/bin/apache-tomcat-9.0.85.tar.gz
sudo tar -xvf apache-tomcat-9.0.85.tar.gz
<img width="1535" height="786" alt="image" src="https://github.com/user-attachments/assets/4e3aff60-e628-441d-b692-d0243587581a" />
Add tomcat credentials in Jenkins Credentials 

Configure the pipeline 

```groovy
pipeline {
    agent any
    tools{
        maven 'maven3'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/BONTHA-SREEVIDHYA/Tomcat.git'
            }
        }
        stage('compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('package') {
            steps {
                sh 'mvn clean package'
            }
        }
         stage('Deploy') {
            steps {
                deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: '72ebabf2-0a4c-4d4d-84b7-0fe0662c6b0b', path: '', url: 'http://3.238.98.5:8080/')], contextPath: 'Planview', war: 'target/*.war'
            }
        }
    }
}

```
Access the tomcat server at <public ip>:8080/
<public ip>:8080/Planview ( as configured tomcat )

Build the pipeline 
<img width="1454" height="636" alt="image" src="https://github.com/user-attachments/assets/9249ed9e-ab7c-46f4-848a-1a6c656335ea" />
<img width="1887" height="1018" alt="image" src="https://github.com/user-attachments/assets/c58defc5-3888-4d5a-8acb-b3f569deb529" />

===========================================================================

Jenkins Troubleshooting:

Status Codes:
128 -- if we don't pass credentials for git checkout 
403 -- Unauthorized ( passed the credentials but incorrect ) || check the credentials configured 
137 -- not enough memory for pipeline 
127 -- not found ( command not found/ tool not found )
ex: mvn package ( if you don't install maven & run - maven not found -- go to tools section & verify the configuration of tool )

Sonar not available ( check if sonar server is available or not ) 
 500 -- Internal Server Error ( nexus / Sonar -- check if nexus is up or not ) // restart the server , check the nexus pod logs 

Sun Security Java Error -- check the jdk version compatibility 

===========================================================================

 Scenario Based Implementation:

 Scenario 1 : Implement a stage only if there are changes in a file 
 <img width="409" height="206" alt="image" src="https://github.com/user-attachments/assets/a54cd4b7-41be-47eb-a3ed-0a73f9155d9d" />
 Test is executed only if there are changes in test.txt file 

 Scenario 2: execution of commands in specific folder 
 <img width="300" height="136" alt="image" src="https://github.com/user-attachments/assets/81886b5c-f3bd-4642-9736-3ffaa90189ac" />

Scenario 3: Multi Environment Deployment 
<img width="909" height="634" alt="image" src="https://github.com/user-attachments/assets/2e436218-fdb0-44b6-9b7c-37cc97eea734" />
<img width="512" height="314" alt="image" src="https://github.com/user-attachments/assets/efbeb326-c858-4b4b-bda6-786791f6dd20" />
Should be enclosed in script 

Scenario 4: Parallel Execution 
<img width="459" height="422" alt="image" src="https://github.com/user-attachments/assets/fb379d5d-ac4d-41a3-92ab-5b3ec6d834e3" />

Scenario 5: Efficient memory management
Publish the artifacts to nexus & clean the workspace( for big jobs) 
<img width="517" height="160" alt="image" src="https://github.com/user-attachments/assets/167b76f8-7fb1-4763-8d93-cfc4379fda5c" />

Scenario 6 : Timeout stages
If a stage is taking more than expected time , job should fail 
<img width="590" height="128" alt="image" src="https://github.com/user-attachments/assets/51dd8e37-710f-442a-9ccf-d9ec43720aa7" />

========================================================================
Outlook Mail Notifications:

Open 587 SMTP Port in Security Group 
Make Sure POP & IMAP are on in outlook mail settings
Create app password ( which can be used by jenkins , also our mail password will not be exposed )
in Jenkins -- plugins -- email extension plugin, stage view 
In Jenkins Settings -- System -- set outlook 
<img width="667" height="246" alt="image" src="https://github.com/user-attachments/assets/76ead7ee-18ef-434a-8239-5090db75515b" />
<img width="1043" height="493" alt="image" src="https://github.com/user-attachments/assets/c9cc917f-3183-433c-bb06-1d28f7db4d48" />
<img width="1018" height="646" alt="image" src="https://github.com/user-attachments/assets/327abef8-fe18-4535-bad4-057b982cdff1" />
<img width="1111" height="511" alt="image" src="https://github.com/user-attachments/assets/871c1a6e-d9d9-401b-9f17-8018fa2a2ec2" />












