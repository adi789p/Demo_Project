pipeline {
    agent any
    
    environment {
        SFDC_INSTANCE_URL = 'https://login.salesforce.com'  // Use appropriate URL based on environment (Production/Sandbox)
        SFDC_USERNAME = credentials('aditya.kumar@gvmantra.com.cpqtrial')  // Use Jenkins credentials to store Salesforce credentials
        CLIENT_ID = credentials('3MVG9WVXk15qiz1La4iWbFc51ux9mIPoA1kPsTRWe7w.zKLUl_A0THzTyEYZeyFlkZFZ6tk68UclVDYvryAHf')     // Use credentials for Client ID
        JWT_KEY_FILE = credentials('jwt-key-file')          // Use Jenkins credentials for JWT key
        GITHUB_REPO = 'https://github.com/your-org/your-repo' // GitHub repository URL
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'your-github-credentials-id', url: "${GITHUB_REPO}"
            }
        }
        
        stage('Install Salesforce CLI') {
            steps {
                script {
                    // Ensure Salesforce CLI is available
                    sh 'sfdx --version'
                }
            }
        }
        
        stage('Authenticate with Salesforce') {
            steps {
                script {
                    // Use JWT for Salesforce authentication
                    sh 'sfdx force:auth:jwt:grant --clientid ${CLIENT_ID} --jwtkeyfile ${JWT_KEY_FILE} --username ${SFDC_USERNAME} --instanceurl ${SFDC_INSTANCE_URL} --setdefaultdevhubusername'
                }
            }
        }
        
        stage('Deploy to Salesforce') {
            steps {
                script {
                    // Deploy the source code to Salesforce
                    sh 'sfdx force:source:deploy --sourcepath force-app --targetusername ${SFDC_USERNAME} --wait 10 --verbose'
                }
            }
        }
    }
    
    post {
        success {
            echo 'Deployment to Salesforce was successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
