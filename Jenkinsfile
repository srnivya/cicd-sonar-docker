pipeline {
    agent any

    environment {
        IMAGE_NAME = "srnivya/cicd-sonar-docker"
        IMAGE_TAG  = "latest"
        DOCKER_CREDS = "dockerhub-creds"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Verify Files') {
            steps {
                bat '''
                echo Listing workspace:
                dir
                '''
            }
        }

        stage('Clean Old Image') {
            steps {
                bat '''
                docker rmi -f %IMAGE_NAME%:%IMAGE_TAG% || exit 0
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                docker build --no-cache -t %IMAGE_NAME%:%IMAGE_TAG% .
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: DOCKER_CREDS,
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                    docker logout
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Image pushed to DockerHub successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
