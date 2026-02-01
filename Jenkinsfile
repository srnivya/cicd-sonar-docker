pipeline {
    agent any

    tools {
        sonarScanner 'SonarScanner'   // Name of SonarScanner in Jenkins
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
        timeout(time: 2, unit: 'MINUTES') {
            waitForQualityGate(abortPipeline: true)
        }
    }
}
    }
}
