pipeline {
    agent any
    
    environment {
        // SonarQube credentials
        SONAR_TOKEN = credentials('sonarqube-token') // Add SonarQube token as Jenkins credential
        SONAR_HOST_URL = 'http://localhost:9000/' // Replace with your SonarQube server URL
        EMAIL_TO = 'dattatray@bioenabletech.com'
        REPORT_FILE = 'failure_report.txt' // File to store the failure report
    }
    
    stages {
        stage('SonarQube Code Scanning') {
            steps {
                // Set up SonarQube Scanner
                script {
                    def scannerHome = tool 'SonarQubeScanner-4.8.0' // Replace with the SonarQube Scanner version you have configured in Jenkins
                    env.PATH = "${scannerHome}/bin:${env.PATH}"
                }

                // Add a failing test case (intentional failure)
                sh 'echo "Failing test: 1/0" > failing_test.py'
                
                // Run SonarQube Scanner with SonarQube credentials
                withSonarQubeEnv('sonarqube-10.1') {
                    sh "sonar-scanner \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.projectKey=smartsuite \
                        -Dsonar.login=${SONAR_TOKEN}"
                }
            }
        }
        stage("Quality gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
    
    post {
        always {
            sh 'echo "this is testing"'
            sh 'rm failing_test.py'
        }
        success {
            // Send an email notification on pipeline success (if needed)
            emailext body: "Jenkins pipeline for code scanning with SonarQube was successful.",
                     subject: "Jenkins Pipeline - Code Scanning Success",
                     to: "${EMAIL_TO}" // Replace with the email address to receive success notifications
        }
        failure {
            // Generate a failure report
            script {
                sh 'echo "Failure Report:" > ${REPORT_FILE}'
                sh 'echo "Pipeline failed due to code quality issues." >> ${REPORT_FILE}'
                sh 'echo "Job URL: ${BUILD_URL}" >> ${REPORT_FILE}'
                // Add more relevant information to the report if needed
            }
            
            // Send an email notification on pipeline failure with the failure report attached
            emailext body: "Jenkins pipeline for code scanning with SonarQube has failed. Please review and fix the issues.\nJob URL: ${BUILD_URL}",
                     subject: "Jenkins Pipeline - Code Scanning Failure",
                     to: "${EMAIL_TO}", // Replace with the email address to receive failure notifications
                     attachmentsPattern: "${REPORT_FILE}" // Attach the generated report to the email
        }
    }
}
