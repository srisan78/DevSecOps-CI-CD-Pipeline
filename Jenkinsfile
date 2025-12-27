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
        
        stage('Building and Pushing to Docker Hub') {
    steps {
        script {
            withDockerRegistry(
                credentialsId: 'dockerhub-pwd',
                url: 'https://index.docker.io/v1/'
            ) {
                sh """
                  docker build -t sridhar76/petshop:${BUILD_NUMBER} .
                  docker push sridhar76/petshop:${BUILD_NUMBER}
                """
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
