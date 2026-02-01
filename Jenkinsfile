pipeline {
    agent any

    tools {
        sonarScanner 'SonarScanner' // Make sure this matches your SonarScanner installation in Jenkins
    }

    environment {
        // Using your actual values
        DOCKER_IMAGE = 'srnivya/cicd-sonar-docker'   // DockerHub repo
        DOCKER_TAG = 'latest'                         // Docker image tag
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'    // Jenkins credential ID for DockerHub
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Using your SonarQube server configured in Jenkins
                withSonarQubeEnv('sonarqube') {
                    sh 'sonar-scanner'
                }
            }
        }

        // ===== Docker Build Stage =====
        stage('Docker Build') {
            steps {
                script {
                    sh "docker build -t $DOCKER_IMAGE:$DOCKER_TAG ."
                }
            }
        }

        // ===== Docker Push Stage =====
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: DOCKERHUB_CREDENTIALS, 
                        usernameVariable: 'DOCKER_USER', 
                        passwordVariable: 'DOCKER_PASS')]) {
                        
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push $DOCKER_IMAGE:$DOCKER_TAG"
                        sh "docker logout"
                    }
                }
            }
        }
    }

    post {
        failure {
            echo '❌ Pipeline faiiled'
        }
        success {
            echo '✅ Pipeline completed successfully'
        }
    }
}
