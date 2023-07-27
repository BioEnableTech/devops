pipeline {
    agent any
    
    tools {
        maven 'M3'
    }
  
    // Environment variables
    environment {
        // ... (existing environment variables)
        SONAR_TOKEN = credentials('sonarqube-token') // Add SonarQube token as Jenkins credential
        SONAR_HOST_URL = 'http://localhost:9000/' // Replace with your SonarQube server URL
        EMAIL_TO = 'dattatray@bioenabletech.com'
        REPORT_FILE = 'failure_report.txt' // File to store the failure report
    }

    stages {
        // ... (existing stages)

        stage('SonarQube analysis') {
            steps {
                script {
                    // requires SonarQube Scanner 2.8+
                    scannerHome = tool 'SonarQubeScanner-4.8.0'
                }

                catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                    withSonarQubeEnv('sonarqube-10.1') {
                      sh "sonar-scanner \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.projectKey=smartsuite \
                        -Dsonar.login=${SONAR_TOKEN}"
                    }
                }
                
                script {
                    // Generate a failure report if catchError block was executed (i.e., if SonarQube analysis failed)
                    if (currentBuild.result == 'FAILURE') {
                        def errorOutput = catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                            // Dummy stage to trigger the catchError block
                            sh 'echo "Intentional Failure"'
                        }
                        def errorMessage = errorOutput.getLog(10).join('\n')
                        sh "echo 'Error message: ${errorMessage}' > ${REPORT_FILE}"
                    }
                }
            }
        }
        
        // ... (remaining stages)
    }
  
    // ... (existing post section)

    // Send an email notification on pipeline failure with the failure report attached
    post {
        always {
            // Cleanup workspace, etc.
            sh 'echo "this is test"'
        }
        success {
            // Send an email notification on pipeline success (if needed)
            emailext body: "Jenkins pipeline for code scanning with SonarQube was successful.",
                     subject: "Jenkins Pipeline - Code Scanning Success",
                     to: "${EMAIL_TO}" // Replace with the email address to receive success notifications
        }
        failure {
            // Send an email notification on pipeline failure with the failure report attached
            emailext body: "Jenkins pipeline for code scanning with SonarQube has failed. Please review and fix the issues.\nJob URL: ${BUILD_URL}",
                     subject: "Jenkins Pipeline - Code Scanning Failure",
                     to: "${EMAIL_TO}", // Replace with the email address to receive failure notifications
                     attachmentsPattern: "${REPORT_FILE}" // Attach the generated report to the email
        }
    }
}
