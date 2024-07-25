Deploying the Swiggy clone app with Jenkins CICD.

Jenkins :
-> create a ec2 instance for Jenkins. ( large , with 30 gb)

For install docker :

sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER  # Replace with your system's username, e.g., 'ubuntu'
newgrp docker
sudo chmod 777 /var/run/docker.sock


For install Jenkins :

sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
openjdk version "17.0.8" 2023-07-18
OpenJDK Runtime Environment (build 17.0.8+7-Debian-1deb12u1)
OpenJDK 64-Bit Server VM (build 17.0.8+7-Debian-1deb12u1, mixed mode, sharing)
 
#jenkins
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins

For check Jenkins is running or not :

systemctl status Jenkins

open port 8080 in EC2. Copy public ip and paste in browser : 8080
-> (for access Jenkins in browser, will get a path for default password )

-> sudo cat /var/lib/jenkins/secrets/initialAdminPassword ( for see the password )

-> paste the password in text field.
-> click on suggested plugin.
-> follow the process for User Name and Password.
-> click on start Jenkins

Copy Clone Url from GitHub : https://github.com/gauravkestwal/a-swiggy-clone.git 

Under Jenkins Dashboard.
-> click on New Item. 
-> give item Name and select Pipeline and click ok

->Dashboard -> select pipline -> go to configure tab -> Paste your piple line script on pipeline text box.
Script : 
pipeline {
    agent any

     stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/gauravkestwal/a-swiggy-clone.git'
            }
        }
        stage("Buildabd deploy docker image") {
        steps {
            script {
                // Build the Docker image
                sh 'docker build -t swegi13 .'
                sh 'docker run -d --name swegi13 -p 3000:3000 swegi13:latest'
            }
        }
     }
    }
}

When we execute it, it will show error , because of Jenkin is not added in Docker group,
TO resolve this issue.
-> If you get docker login failed errorr

In terminal execute these commands : 
sudo su
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins




 







