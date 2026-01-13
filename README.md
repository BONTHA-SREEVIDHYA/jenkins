# jenkins

Pre-requisite for **installing Jenkins **is JDK 
If ubuntu : sudo apt install -y openjdk-17-jdk-headless

vi Jenkins.sh
          sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
            https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
          echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
            https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
            /etc/apt/sources.list.d/jenkins.list > /dev/null
          sudo apt update
          sudo apt install jenkins

chmod +x Jenkins.sh 
./Jenkins.sh
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

Can Access the Jenkins UI at <public_ip_ec2>:8080
You can fetch the initialAdminPassword at **sudo cat /var/lib/jenkins/secrets/initialAdminPassword** 
