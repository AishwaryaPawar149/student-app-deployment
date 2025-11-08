# 🦑‍👨‍💻 Student App Deployment using Jenkins, Maven, and Tomcat

## 📘 Project Overview

This project demonstrates a **CI/CD pipeline** built with **Jenkins** to automate the deployment of a **Java-based web application** onto a **Tomcat server** hosted on an **AWS EC2 instance**.
The pipeline fetches code from **GitHub**, builds the WAR file using **Maven**, and deploys it automatically to the remote server.

---

## ⚙️ Tech Stack

* **AWS EC2** – Host Jenkins and Tomcat servers
* **Jenkins** – Continuous Integration and Deployment tool
* **Maven** – Build automation for Java project
* **Tomcat10** – Application server to host the WAR file
* **GitHub** – Version control and source code repository
* **SSH** – Secure communication between Jenkins and EC2

---

## 🚀 CI/CD Pipeline Workflow

1. **Checkout Source Code:** Jenkins pulls the latest code from the GitHub repository.
2. **Build WAR File:** Maven compiles the project and packages it into a `.war` file.
3. **Deploy to Tomcat:** Jenkins securely transfers the WAR to the EC2 server via SSH.
4. **Restart Tomcat:** The Tomcat service restarts to serve the updated application.
5. **Access the App:** The deployed app runs at `http://<EC2-IP>:8080/`.

---

## 🧬 Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        SERVER_IP = '172.31.4.60'
        TOMCAT_PATH = '/var/lib/tomcat10/webapps'
        SSH_CRED_ID = 'pull-key'
        TOMCAT_SVC = 'tomcat10'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AishwaryaPawar149/student-app-deployment.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sshagent(credentials: [env.SSH_CRED_ID]) {
                    sh '''
                        WAR_FILE=$(ls target/*.war | head -n 1)
                        scp -o StrictHostKeyChecking=no $WAR_FILE ubuntu@${SERVER_IP}:/tmp/
                        ssh -o StrictHostKeyChecking=no ubuntu@${SERVER_IP} '
                            sudo rm -rf ${TOMCAT_PATH}/*
                            sudo mv /tmp/*.war ${TOMCAT_PATH}/ROOT.war
                            sudo chown tomcat:tomcat ${TOMCAT_PATH}/ROOT.war
                            sudo systemctl restart ${TOMCAT_SVC}
                        '
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ App deployed successfully! Visit: http://${SERVER_IP}:8080/"
        }
    }
}
```

---

## 🌈 Infrastructure Architecture

* **Jenkins EC2 Instance** → Builds and deploys code
* **Tomcat EC2 Instance** → Hosts the deployed application
* **GitHub Repository** → Stores source code
* **Maven** → Builds WAR file

---

## 🧠 Learning Outcome

* Gained experience with **CI/CD automation using Jenkins**
* Learned **Maven build process** for Java applications
* Configured **Tomcat on AWS EC2**
* Automated deployment via **SSH and Jenkinsfile**

---

## 👩‍💻 Author

**Aishwarya Pawar**
GitHub: [AishwaryaPawar149](https://github.com/AishwaryaPawar149)
