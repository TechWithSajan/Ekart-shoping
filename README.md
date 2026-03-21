# 🚀 CI/CD Pipeline Project – Jenkins + SonarQube + Nexus + Docker + EKS

![CI/CD](https://img.shields.io/badge/CI-CD%20Pipeline-blue)
![Jenkins](https://img.shields.io/badge/Jenkins-Automation-red)
![Docker](https://img.shields.io/badge/Docker-Containerization-blue)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Orchestration-blue)
![AWS](https://img.shields.io/badge/AWS-EKS-orange)

---

## 📌 Project Overview

This DevSecOps project demonstrates a complete **End-to-End CI/CD Pipeline** for a secure Java-based application using modern DevOps tools.

### 🔄 Pipeline Flow

```
GitHub → Jenkins → SonarQube → OWASP → Nexus → Docker → Kubernetes (EKS)
```
---
## 📁 Repositories


Server creation Repo:

https://github.com/TechWithSajan/Server-Creation-Jenkins-Nexus-Sonar.git
	
KS Setup:
https://github.com/TechWithSajan/EKS-cluster-deployment.git

Application Repo:
https://github.com/TechWithSajan/Ekart-shoping.git


---
---

## 🧱 Architecture

* **Source Code**: GitHub
* **CI/CD Tool**: Jenkins
* **Code Quality**: SonarQube
* **Security Scan**: OWASP Dependency Check
* **Artifact Repository**: Nexus
* **Containerization**: Docker
* **Orchestration**: Kubernetes (AWS EKS)

---

## 🖥️ Infrastructure Setup

| Component | Instance Type | OS     |
| --------- | ------------- | ------ |
| Jenkins   | t3.medium     | Ubuntu |
| SonarQube | t3.medium     | Ubuntu |
| Nexus     | t3.medium     | Ubuntu |

---

## ⚙️ Jenkins Setup

### Install Java & Jenkins

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y

wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y

sudo systemctl start jenkins
sudo systemctl enable jenkins
```

---

### Install Docker

```bash
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
sudo systemctl restart docker
```

---

## 🔍 SonarQube Setup

```bash
docker pull sonarqube:latest
docker run -d -p 9000:9000 sonarqube
```
1st time UI Acsses using below credential 

👉 Access: `http://<sonar-ip>:9000`

username :- admin

default pwd : admin

and Set new pwd 
New pwd : *******


---

## 📦 Nexus Setup

```bash
docker run -d -p 8081:8081 sonatype/nexus3
```

Retrieve admin password:

```bash

login the nexus instnace 

docker ps --> find the container

docker exec -it <container_id> /bin/bash

docker exec -it 6dba1b14e8f5 /bin/bash

bash-4.4$ cd /nexus-data/
bash-4.4$ cat admin.password

5c3bfc44-0df7-46d0-8cae-3ec0d5264e8d

Nexus uid : admin
New Pwd : ****


```

---

## 🔌 Jenkins Plugins

Go to jenkins - manage jenkins- 

Install below Plugins

1. SonarQube Scanner
2. Nexus Artifact Uploader
3. Docker
4. docker-build-step
5. docker pipeline
6. OWASP Dependency-Check
7. Eclipse Temurin installer
8. Pipeline Maven Integration


---

## 🧰 Jenkins Tool Configuration

| Tool            | Name          |
| --------------- | ------------- |
| JDK             | jdk-17        |
| Maven           | maven3        |
| Sonar Scanner   | sonar-scanner |
| DependencyCheck | DC            |

jenkins-> tools-> add Jdk-17 -> install automatically-> Install from adoptium.net -jdk17.09+9
   
--> configure scanner - sonar-scanner - default version

--> maven - maven3  ->   3.6.3

--> dependency check installation - DC - 6.5.1

--> docker - latest

---

## 🔐 Credentials Setup in Jenkins 

setup as secret text (pwd) and below ID

| Credential         | ID            |
| ------------------ | ------------- |
| Sonar Token        | sonar-token   |
| DockerHub Password | dockerhub-pwd |
| NVD API Key        | nvd-api-key   |


---

## 🔗 Integrations

### SonarQube Sonar -Security

* URL: `http://<sonar-ip>:9000`
* Token: `sonar-token`
  
Go to the Sonar UI portal - >Token >- Admin- Security- Users

Create credentials for sonar-token 

Give the name for token --> sonar-token 

and generate the token and copy 

squ_893ee4e66e059ea3ddebca29132c4b219c61d0f3 

---
Docker hub integrtion  in jenkins

Docker login
Docker username : 

Id will be from Devloper code jenkins file 

Secret text : --> docker hub pwd 

ID :  dockerhub-pwd

---

---
NVD- Api-key credintial :- 

Id : nvd-api-key

Key genarte using below link and create the credinatl in Jenkins 

https://nvd.nist.gov/developers/request-an-api-key

d1aec818-25dd-43d5-8b3f-4384f235e862

---
---

Jenkins - Sonar - Integration

Manage Jenkins -> System -> SonarQube installations

Name : sonar
Server -URL :
http://3.109.5.205:9000/

---

---

### Nexus (pom.xml)

```xml

Jenkins - Nexus - Integration

Open your project code and edit POM.xml for nexus configuration

http://15.207.85.17:8081

Manage Jenkins -> Managed files -> Add new config -> Global Maven settings.xml->  global-maven

Add Line 119 in xml 

 
      <server>
      <id>maven-releases</id>
      <username>admin</username>
      <password>Parth@123456</password>
      </server>

      <server>
      <id>maven-snapshots</id>
      <username>admin</username>
      <password>Parth@123456</password>
      </server>

```

---

================================Shopping-Ekart Deployment ===================================

https://github.com/TechWithSajan/Ekart-shoping.git

---

## ☸️ Kubernetes (EKS Setup)

```bash
aws eks update-kubeconfig --region ap-south-1 --name Tech-data-cluster
kubectl apply -f deploymentservice.yml
```

---

## 📊 Useful Commands

```bash
 kubectl get node
 kubectl get pods
 kubectl describe pod 
kubectl get all | grep ekart
kubectl get deployments
kubectl edit deployment ekart-deployment
kubectl get svc
kubectl describe svc ekart-shoping-ssvc
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl get svc ekart-shoping-ssvc -o wide


kubectl delete deployment ekart-deployment
kubectl delete service ekart-shoping-ssvc


```

---

## 🔄 Jenkins Pipeline Stages

1. Checkout Code
2. Build (Maven)
3. Test
4. SonarQube Analysis
5. OWASP Scan
6. Package Artifact
7. Upload to Nexus
8. Build Docker Image
9. Push to DockerHub
10. Deploy to EKS

---




---

## 🎯 Key Features

✅ Fully automated CI/CD pipeline
✅ Code quality analysis
✅ Security scanning
✅ Artifact management
✅ Containerized deployment
✅ Kubernetes orchestration

---

## 👨‍💻 Author

**TechDataInfinity DevOps Project**

---

