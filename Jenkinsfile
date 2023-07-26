pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                // Clone the repository into the workspace
                git 'https://github.com/BioEnableTech/devops.git'
            }
        }
        
        
        stage('SonarQube Code Scanning') {
            environment {
                // Set SonarQube server URL and access token
                SONAR_HOST_URL = 'http://localhost:9000/'
                SONAR_TOKEN = credentials('sonarqube-token') // Add SonarQube token as Jenkins credential
            }
             stage('SonarQube analysis') {
      steps {
        script {
          // requires SonarQube Scanner 2.8+
          scannerHome = tool 'SonarQubeScanner-4.8.0'
        }
        withSonarQubeEnv('sonarqube-10.1') {
			sh "${scannerHome}/bin/sonar-scanner \
			 -Dsonar.host.url=http:localhost:9000 \
 			 -Dsonar.projectKey=smartsuite \
  			 -Dsonar.login=admin \
			 -Dsonar.password=test"
		}
 			
 			
		
		
    
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