pipeline {
    agent any
    
    environment {
        // GitHub credentials
        GITHUB_CREDENTIALS = credentials('multi-repo') // Add your GitHub credentials as Jenkins credential
        
        // SonarQube credentials
        SONAR_TOKEN = credentials('sonarqube-token') // Add SonarQube token as Jenkins credential
        SONAR_HOST_URL = 'http://localhost:9000/' // Replace with your SonarQube server URL
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Clone the repository into the workspace using GitHub credentials
                git credentialsId: "${GITHUB_CREDENTIALS}", url: 'https://github.com/BioEnableTech/devops'
            }
        }
        
        stage('SonarQube Code Scanning') {
            steps {
                // Set up SonarQube Scanner
                script {
                    def scannerHome = tool 'SonarQubeScanner-4.8.0' // Replace with the SonarQube Scanner version you have configured in Jenkins
                    env.PATH = "${scannerHome}/bin:${env.PATH}"
                }
                
                // Run SonarQube Scanner with SonarQube credentials
                withSonarQubeEnv('sonarqube-10.1') {
                    sh "sonar-scanner \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.projectKey=your_project_key \
                        -Dsonar.login=${SONAR_TOKEN}"
                }
            }
        }
    }
    
    post {
        always {
                sh 'echo "this is testing"'
        }
        success {
            // Send an email notification on pipeline success (if needed)
            emailext body: "Jenkins pipeline for code scanning with SonarQube was successful.",
                     subject: "Jenkins Pipeline - Code Scanning Success",
                     to: "dattatray@bioenabletech.com" // Replace with the email address to receive success notifications
        }
        failure {
            // Send an email notification on pipeline failure
            emailext body: "Jenkins pipeline for code scanning with SonarQube has failed. Please review and fix the issues.",
                     subject: "Jenkins Pipeline - Code Scanning Failure",
                     to: "dattatray@bioenabletech.com" // Replace with the email address to receive failure notifications
        }
    }
}
