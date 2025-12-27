pipeline {
    agent any

    tools {
        maven 'mymaven'
    }

    environment {
        SONARQUBE_SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Cleaning Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Code Checkout') {
            steps {
                git 'https://github.com/srisan78/DevSecOps-CI-CD-Pipeline.git'
            }
        }

        stage('Compiling Maven') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Testing') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh """
                    ${SONARQUBE_SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=Petshop \
                    -Dsonar.projectKey=Petshop \
                    -Dsonar.java.binaries=target
                    """
                }
            }
        }

        stage('Building Docker Image') {
            steps {
                sh "docker build -t sridhar76/petshop:${BUILD_NUMBER} ."
            }
        }

        stage('Building and Pushing to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(
                        credentialsId: 'dockerhub-pwd',
                        url: 'https://index.docker.io/v1/'
                    ) {
                        sh """
                          docker push sridhar76/petshop:${BUILD_NUMBER}
                        """
                    }
                }
            }
        }

        stage('Image Scanning using TRIVY') {
            steps {
                sh "trivy image sridhar76/petshop:${BUILD_NUMBER} > trivy.txt"
            }
        }
    }
}
