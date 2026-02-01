pipeline {
    agent any

    tools {
        sonarScanner 'SonarScanner'
    }

    environment {
        DOCKER_IMAGE = 'srnivya/cicd-sonar-docker'
        DOCKER_TAG = 'latest'
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        SONARQUBE_SERVER = 'sonarqube'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    bat '''
                    sonar-scanner ^
                    -Dsonar.projectKey=cicd-sonar-docker ^
                    -Dsonar.projectName=cicd-sonar-docker ^
                    -Dsonar.sources=.
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat '''
                docker version
                docker build -t %DOCKER_IMAGE%:%DOCKER_TAG% .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: DOCKERHUB_CREDENTIALS,
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                bat '''
                docker push %DOCKER_IMAGE%:%DOCKER_TAG%
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}
