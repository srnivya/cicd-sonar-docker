pipeline {
    agent any

    tools {
        sonarScanner 'SonarScanner'
    }

    environment {
        DOCKER_IMAGE = 'sonarsource/sonar-scanner-cli:latest' // Sonar Scanner Docker image
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    docker.image(DOCKER_IMAGE).inside {
                        withSonarQubeEnv('sonarqube') {
                            sh 'sonar-scanner'
                        }
                    }
                }
            }
        }
    }
}
