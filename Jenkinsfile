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

    triggers {
        githubPush()  // Trigger the build on push events to GitHub (including PR updates)
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Debugging: print out the environment variables
                    echo "GIT_BRANCH: ${env.GIT_BRANCH}"
                    echo "CHANGE_ID: ${env.CHANGE_ID}"
                    
                    def prBranch = env.GIT_BRANCH ?: "refs/pull/${env.CHANGE_ID}/merge"
                    echo "PR Branch: ${prBranch}"
                    git credentialsId: 'github-pat', url: "${GITHUB_REPO}", branch: prBranch
                }
            }
        }

        stage('Install Salesforce CLI') {
            steps {
                script {
                    // Verify Salesforce CLI is installed
                    bat """
                    echo Verifying Salesforce CLI installation...
                    sfdx --version
                    """
                }
            }
        }

        stage('Authenticate with Salesforce') {
            steps {
                script {
                    // Authenticate to Salesforce using JWT
                    bat """
                    echo Authenticating to Salesforce...
                    sfdx force:auth:jwt:grant --clientid "${CLIENT_ID}" --jwtkeyfile "${JWT_KEY_FILE}" --username "${SFDC_USERNAME}" --instanceurl "${SFDC_INSTANCE_URL}" --setdefaultdevhubusername
                    """
                }
            }
        }

        stage('Validate Changes') {
            steps {
                script {
                    // Validate the changes in the PR
                    bat """
                    echo Validating changes in PR...
                    sfdx force:source:deploy --sourcepath force-app --targetusername "${SFDC_USERNAME}" --wait 10 --validateonly --verbose
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'PR validation was successful!'
        }
        failure {
            echo 'PR validation failed.'
        }
    }
}
