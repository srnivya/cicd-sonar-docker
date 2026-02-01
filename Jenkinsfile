pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token')   // Jenkins credential ID
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/your/repo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh """
                       mvn sonar:sonar \
                       -Dsonar.projectKey=demo \
                       -Dsonar.host.url=http://192.168.1.7:9000 \
                       -Dsonar.login=$SONAR_TOKEN
                       """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo 'Waiting for SonarQube Quality Gate result...'
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        failure {
            echo '❌ Build failed due to SonarQube Quality Gate'
        }
        success {
            echo '✅ Build passed Quality Gate'
        }
    }
}
