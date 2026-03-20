pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        NVD_API_KEY = credentials('nvd-api-key')  // Jenkins secret text credential
    }

    tools {
        maven 'maven3'
        jdk 'Jdk-17'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/TechWithSajan/Ekart-shoping.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }

        stage('unit tests') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh """
                    ${env.SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=Ekart-shoping \
                    -Dsonar.projectName=Ekart-shoping \
                    -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                  withCredentials([string(credentialsId: 'nvd-api-key', variable: 'NVD_API_KEY')]) {
                    dependencyCheck additionalArguments: "--nvdApiKey=$NVD_API_KEY",
                                    odcInstallation: 'DC'
             }
        }
        }

        stage('Build Artifact') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withMaven(
                    maven: 'maven3',
                    jdk: 'Jdk-17',
                    globalMavenSettingsConfig: 'global-maven'
                ) {
                    sh "mvn clean deploy -DskipTests=true"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t techdatainfinity/ekart-shoping:latest -f docker/Dockerfile ."
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                    sh """
                    echo $dockerhubpwd | docker login -u techdatainfinity --password-stdin
                    docker push techdatainfinity/ekart-shoping:latest
                    """
                }
            }
        }

        stage('Configure EKS') {
            steps {
                sh 'aws eks update-kubeconfig --region ap-south-1 --name Tech-data-cluster'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deploymentservice.yml'
            }
        }
    }
}
