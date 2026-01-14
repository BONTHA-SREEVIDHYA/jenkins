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



