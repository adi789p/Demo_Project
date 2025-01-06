pipeline {
    agent any
    
    environment {
        SFDC_INSTANCE_URL = 'https://login.salesforce.com'
        SFDC_USERNAME = 'aditya.kumar@gvmantra.com.cpqtrial'
        CLIENT_ID = '3MVG9WVXk15qiz1La4iWbFc51ux9mIPoA1kPsTRWe7w.zKLUl_A0THzTyEYZeyFlkZFZ6tk68UclVDYvryAHf'
        JWT_KEY_FILE = credentials('eb15b08c-6cc2-4e5f-a01e-9614ae86a0b4')
        GITHUB_REPO = 'https://github.com/adi789p/Demo_Project'
        PATH = "C:\\Windows\\System32;C:\\Program Files\\Salesforce CLI\\bin;${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Just this line will do the job
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
