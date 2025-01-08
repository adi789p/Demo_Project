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
                script {
                    // Clone the GitHub repository using the correct credentials and specify the branch
                    git credentialsId: 'github-pat', url: "${GITHUB_REPO}", branch: 'develop'
                }
            }
        }


        stage('Process Branch') {
            steps {
                script {
                    // Detect branch type
                    if (env.BRANCH_NAME.startsWith('feature/')) {
                        echo "Feature branch detected: ${env.BRANCH_NAME}"
                        currentBuild.description = "Feature Branch Validation"
                    } else if (env.BRANCH_NAME == 'develop') {
                        echo "Develop branch detected: Validation and Deployment"
                        currentBuild.description = "Develop Branch Deployment"
                    } else {
                        error("Unsupported branch: ${env.BRANCH_NAME}")
                    }
                }
            }
        }

        stage('Validate Changes') {
            steps {
                script {
                    // Validate feature or develop branch
                    bat """
                    echo Validating branch ${env.BRANCH_NAME}...
                    sfdx project deploy start --target-org "${SFDC_USERNAME}" --wait 10 --checkonly --verbose
                    """
                }
            }
        }

        stage('Deploy to Salesforce (Develop Only)') {
            when {
                branch 'develop' // Only deploy if the branch is 'develop'
            }
            steps {
                script {
                    bat """
                    echo Deploying develop branch to Salesforce...
                    sfdx sfdx project deploy start force-app --targetusername "${SFDC_USERNAME}" --wait 10 --verbose
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}


