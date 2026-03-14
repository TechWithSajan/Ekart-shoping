pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    tools {
        maven 'maven3'
        jdk 'jdk-17'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/TechWithSajan/Ekart-shoping.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test -DskipTests=true'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=Ekart-shoping \
                    -Dsonar.projectName=Ekart-shoping \
                    -Dsonar.java.binaries=target/classes
                    '''
                }
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('Build Artifact') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withMaven(
                    maven: 'maven3',
                    jdk: 'jdk-17',
                    globalMavenSettingsConfig: 'global-maven'
                ) {
                    sh 'mvn deploy -DskipTests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t techdatainfinity/ekart-shoping:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    docker login -u $DOCKER_USER -p $DOCKER_PASS
                    docker push techdatainfinity/ekart-shoping:latest
                    '''
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
