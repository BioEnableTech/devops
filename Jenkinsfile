pipeline {
    agent any
    
    environment {
        // SonarQube credentials
        SONAR_TOKEN = credentials('sonarqube-token') // Add SonarQube token as Jenkins credential
        SONAR_HOST_URL = 'http://locahost:900/' // Replace with your SonarQube server URL
        EMAIL_TO = 'dattatray@bioenabletech.com'
    }
    
    stages {
        stage('SonarQube Code Scanning') {
            steps {
                // Set up SonarQube Scanner
                script {
                    def scannerHome = tool 'SonarQubeScanner-4.8.0' // Replace with the SonarQube Scanner version you have configured in Jenkins
                    env.PATH = "${scannerHome}/bin:${env.PATH}"
                }

                sh 'echo "intentional failure" > test_failure.txt'
                
                // Run SonarQube Scanner with SonarQube credentials
                withSonarQubeEnv('sonarqube-10.1') {
                    sh "sonar-scanner \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.projectKey=smartsuite \
                        -Dsonar.login=${SONAR_TOKEN}"
                }
            }
        }
    }
    
    post {
        always {
            sh 'echo "this is testing"'
            sh 'rm test_failure.txt'
        }
        success {
            // Send an email notification on pipeline success (if needed)
            emailext body: "Jenkins pipeline for code scanning with SonarQube was successful Please review and fix the issues.\nJob URL: ${BUILD_URL}",
                     subject: "Jenkins Pipeline - Code Scanning Success",
                     to: "${EMAIL_TO}"// Replace with the email address to receive success notifications
        }
        failure {
            // Send an email notification on pipeline failure
            emailext body: "Jenkins pipeline for code scanning with SonarQube has failed. Please review and fix the issues.\nJob URL: ${BUILD_URL}",
                     subject: "Jenkins Pipeline - Code Scanning Failure",
                     to: "${EMAIL_TO}" // Replace with the email address to receive failure notifications
        }
    }
}
