pipeline {
    agent any
    
    stages {
        
        stage('SonarQube Code Scanning') {
            environment {
                // Set SonarQube server URL and access token
                SONAR_HOST_URL = 'http://localhost:9000/'
                SONAR_TOKEN = credentials('sonarqube-token') // Add SonarQube token as Jenkins credential
            }
            steps {
                script {
                    // Set the SonarQube Scanner version (requires SonarQube Scanner 2.8+)
                    scannerHome = tool 'SonarQubeScanner-4.8.0'
                }
                // Use the SonarQube Scanner with appropriate environment settings
                withSonarQubeEnv('sonarqube-10.1.0.73491') {
                    sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.projectKey=smartsuite \
                        -Dsonar.password=test"
                }
            }
        }
    }
    
    post {
        always {
            // Cleanup workspace, etc.
            sh 'echo "this is testing"'
        }
        success {
            // Send an email notification on pipeline success (if needed)
            emailext body: "Jenkins pipeline for code scanning with SonarQube was successful.",
                     subject: "Jenkins Pipeline - Code Scanning Success",
                     to: "dattatray@bioenabletech.com"
        }
        failure {
            // Send an email notification on pipeline failure
            emailext body: "Jenkins pipeline for code scanning with SonarQube has failed. Please review and fix the issues.",
                     subject: "Jenkins Pipeline - Code Scanning Failure",
                     to: "dattatray@bioenabletech.com"
        }
    }
}
