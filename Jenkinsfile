pipeline {
    agent any

    tools {
        sonarScanner 'SonrScanner'   // Name of SonarScanner in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Run SonarQube scan with Jenkins environment
                withSonarQubeEnv('sonarqube') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Wait for Quality Gate result
                timeout(time: 5, unit: 'MINUTES') {
                    // Fail the build if the Quality Gate is not passed
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
