# 🚀 End-to-End CI/CD Implementation Guide

**Jenkins + SonarQube + Nexus + Docker + EKS (Kubernetes)**

---

## 📌 Architecture Overview

```
GitHub → Jenkins → SonarQube → OWASP → Nexus → Docker → Kubernetes (EKS)
```

---

## 🖥️ STEP 1: Launch Infrastructure

Create **3 Ubuntu EC2 instances (t3.medium):**

* Jenkins Server
* SonarQube Server
* Nexus Server

---

## ⚙️ STEP 2: Setup Jenkins Server

### Install Java & Jenkins

```bash
sudo apt update -y
sudo apt install openjdk-17-jdk -y

java -version
```

```bash
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

### Install Docker on Jenkins

```bash
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
sudo systemctl restart docker
```

---

## 🔍 STEP 3: Setup SonarQube Server

```bash
sudo apt update -y
sudo apt install docker.io -y

docker pull sonarqube:latest
docker run -d -p 9000:9000 sonarqube:latest
```

Access:

```
http://<sonar-ip>:9000
```

* Username: admin
* Password: admin → change password

---

## 📦 STEP 4: Setup Nexus Repository

```bash
sudo apt update -y
sudo apt install docker.io -y

docker run -d -p 8081:8081 sonatype/nexus3
```

### Get Nexus Password

```bash
docker ps
docker exec -it <container_id> /bin/bash
cat /nexus-data/admin.password
```

Access:

```
http://<nexus-ip>:8081
```

---

## 🔌 STEP 5: Configure Jenkins Plugins

Install the following plugins:

* SonarQube Scanner
* Nexus Artifact Uploader
* Docker Pipeline
* OWASP Dependency Check
* Eclipse Temurin Installer
* Pipeline Maven Integration

---

## 🧰 STEP 6: Configure Jenkins Tools

Go to: **Manage Jenkins → Global Tool Configuration**

Add:

* JDK → `jdk-17`
* Maven → `maven3`
* Sonar Scanner → `sonar-scanner`
* Dependency Check → `DC`

⚠️ Important:

* Do NOT use auto-install for JDK
* Set manual path:

```
/usr/lib/jvm/java-17-openjdk-amd64
```

---

## 🔐 STEP 7: Add Credentials in Jenkins

Add the following credentials:

### Sonar Token

* ID: `sonar-token`

### DockerHub Password

* ID: `dockerhub-pwd`

### NVD API Key

* ID: `nvd-api-key`

Generate API Key:
https://nvd.nist.gov/developers/request-an-api-key

---

## 🔗 STEP 8: Integrations

### SonarQube Integration

Manage Jenkins → System

* Name: `sonar`
* URL: `http://<sonar-ip>:9000`
* Token: `sonar-token`

---

### Nexus Integration

#### Update `pom.xml`

```xml
<distributionManagement>
  <snapshotRepository>
    <id>maven-snapshots</id>
    <url>http://<nexus-ip>:8081/repository/maven-snapshots/</url>
  </snapshotRepository>
</distributionManagement>
```

---

#### Jenkins Managed File (settings.xml)

```xml
<server>
  <id>maven-releases</id>
  <username>admin</username>
  <password>******</password>
</server>

<server>
  <id>maven-snapshots</id>
  <username>admin</username>
  <password>******</password>
</server>
```

---

## 🐳 STEP 9: Docker Setup

```bash
docker build -t techdatainfinity/ekart-shoping .
docker login
docker push techdatainfinity/ekart-shoping
```

---

## ☸️ STEP 10: Setup EKS Cluster

Use repository:

```
https://github.com/TechWithSajan/EKS-cluster-deployment.git
```

### Configure kubectl

```bash
aws eks update-kubeconfig --region ap-south-1 --name Tech-data-cluster
```

---

## 🚀 STEP 11: Deploy to Kubernetes

```bash
kubectl apply -f deploymentservice.yml
```

---

## 📊 Useful Kubernetes Commands

```bash
kubectl get nodes
kubectl get pods
kubectl get svc
kubectl describe svc ekart-shoping-ssvc
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

## 🔄 STEP 12: Jenkins Pipeline Flow

1. Git Checkout
2. Compile
3. Test
4. SonarQube Analysis
5. OWASP Scan
6. Build JAR
7. Deploy to Nexus
8. Build Docker Image
9. Push to DockerHub
10. Deploy to EKS

---

## ⚠️ Troubleshooting

### JDK Error

* Remove `openjdk-21-jre`
* Use only `jdk-17`
* Configure manually

---

### OWASP 403 Error

Add API key:

```bash
--nvdApiKey <your-key>
```

---

### Nexus 401 Unauthorized

* Verify credentials in:

  * Jenkins
  * settings.xml
  * pom.xml

---

## ✅ Final Outcome

* Code pushed → Pipeline triggered
* Build + Scan completed
* Artifact stored in Nexus
* Docker image pushed
* Application deployed to Kubernetes (EKS)

---

## 📁 Project Repository

* App Repo: https://github.com/TechWithSajan/Ekart-shoping.git
* Infra Repo: https://github.com/TechWithSajan/EKS-cluster-deployment.git

---

## 🎯 Author

**TechDataInfinity CI/CD Implementation**
