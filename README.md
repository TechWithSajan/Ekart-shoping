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

username :- admin

default pwd : admin

and Set new pwd 
New pwd : *******


👉 Access: `http://<sonar-ip>:9000`

---

## 📦 Nexus Setup

```bash
docker run -d -p 8081:8081 sonatype/nexus3
```

Retrieve admin password:

```bash
docker exec -it <container_id> /bin/bash
cat /nexus-data/admin.password
```

---

## 🔌 Jenkins Plugins

Install:

* SonarQube Scanner
* Nexus Artifact Uploader
* Docker Pipeline
* OWASP Dependency Check
* Eclipse Temurin Installer
* Pipeline Maven Integration

---

## 🧰 Jenkins Tool Configuration

| Tool            | Name          |
| --------------- | ------------- |
| JDK             | jdk-17        |
| Maven           | maven3        |
| Sonar Scanner   | sonar-scanner |
| DependencyCheck | DC            |

⚠️ Use manual JDK path:

```
/usr/lib/jvm/java-17-openjdk-amd64
```

---

## 🔐 Credentials Setup

| Credential         | ID            |
| ------------------ | ------------- |
| Sonar Token        | sonar-token   |
| DockerHub Password | dockerhub-pwd |
| NVD API Key        | nvd-api-key   |

---

## 🔗 Integrations

### SonarQube

* URL: `http://<sonar-ip>:9000`
* Token: `sonar-token`

---

### Nexus (pom.xml)

```xml
<distributionManagement>
  <snapshotRepository>
    <id>maven-snapshots</id>
    <url>http://<nexus-ip>:8081/repository/maven-snapshots/</url>
  </snapshotRepository>
</distributionManagement>
```

---

## 🐳 Docker Workflow

```bash
docker build -t techdatainfinity/ekart-shoping .
docker login
docker push techdatainfinity/ekart-shoping
```

---

## ☸️ Kubernetes (EKS Setup)

```bash
aws eks update-kubeconfig --region ap-south-1 --name Tech-data-cluster
kubectl apply -f deploymentservice.yml
```

---

## 📊 Useful Commands

```bash
kubectl get nodes
kubectl get pods
kubectl get svc
kubectl describe svc ekart-shoping-ssvc
kubectl get events --sort-by=.metadata.creationTimestamp
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

## ⚠️ Troubleshooting

### ❌ JDK Installation Error

* Avoid auto-install
* Configure manually

---

### ❌ OWASP 403 Error

Add API key:

```bash
--nvdApiKey=<your-key>
```

---

### ❌ Nexus 401 Error

* Verify credentials in:

  * Jenkins
  * settings.xml
  * pom.xml

---

## 📁 Repositories

* 🔗 Application:
  https://github.com/TechWithSajan/Ekart-shoping

* 🔗 EKS Setup:
  https://github.com/TechWithSajan/EKS-cluster-deployment

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

## ⭐ Support

If you like this project, give it a ⭐ on GitHub!
