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





