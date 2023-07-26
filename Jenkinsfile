pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                // Clone the repository into the workspace
                git 'https://github.com/your_username/your_repository.git'
            }
        }
        
        
        stage('SonarQube Code Scanning') {
            environment {
                // Set SonarQube server URL and access token
                SONAR_HOST_URL = 'http://localhost:9000/'
                SONAR_TOKEN = credentials('sonarqube-token') // Add SonarQube token as Jenkins credential
            }
            steps {
                // Run SonarQube Scanner with necessary options
                script {
                    sh "sonar-scanner \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=$SONAR_TOKEN \
                        -Dsonar.projectKey=your_project_key \
                        -Dsonar.sources=src"
                }
            }
        }
    }
    
  
        
        failure {
            // Send an email notification on pipeline failure
            emailext body: "Jenkins pipeline for code scanning with SonarQube has failed. Please review and fix the issues.",
                     subject: "Jenkins Pipeline - Code Scanning Failure",
                     to: "dattatray@bioenabletech.com"
        }
    }
}
