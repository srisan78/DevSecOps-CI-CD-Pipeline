 pipeline{
    agent any
    tools{
        maven 'mymaven'

    }
    environment{
            SONARQUBE_SCANNER_HOME = tool 'sonar-scanner'
        }
    stages{
        stage('cleaning workspace'){
            steps{
                deleteDir()
            }
        }
        stage('Code Checkout') {
            steps {
                git 'https://github.com/srisan78/DevSecOps-CI-CD-Pipeline.git'
            }
        }
        stage('compaling maven'){
            steps{
                sh 'mvn clean compile'
            }
        }
        stage('unit testing'){
            steps{
                sh 'mvn test'
            }
        }
        
        stage('sonarqube analysis'){
            steps{
                withSonarQubeEnv('sonarqube') {
            sh '''
            $SONARQUBE_SCANNER_HOME/bin/sonar-scanner \
            -Dsonar.projectName=Petshop \
            -Dsonar.projectKey=Petshop \
            -Dsonar.java.binaries=target
            '''
                }
            }
        }
        
        stage ('Building and pushing to docker hub'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub-pwd', toolName: 'docker') {
                        sh "docker build -t sridhar76/petshop:${BUILD_TAG} ."
                        sh "docker push sridhar76/petshop:${BUILD_TAG}"
                   }
                }
            }
        }
        stage("Image Scanning using TRIVY"){
            steps{
                sh "trivy image sridhar76/petshop:${BUILD_TAG} > trivy.txt"
            }
        }
    }
}
