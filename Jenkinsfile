pipeline {
    agent any

    environment {
        SFDC_INSTANCE_URL = 'https://login.salesforce.com'
        SFDC_USERNAME = 'aditya.kumar@gvmantra.com.cpqtrial'
        CLIENT_ID = '3MVG9WVXk15qiz1La4iWbFc51ux9mIPoA1kPsTRWe7w.zKLUl_A0THzTyEYZeyFlkZFZ6tk68UclVDYvryAHf'
        JWT_KEY_FILE = credentials('eb15b08c-6cc2-4e5f-a01e-9614ae86a0b4') // Ensure the correct credential ID is used
        GITHUB_REPO = 'https://github.com/adi789p/Demo_Project'
        PATH = "C:\\Windows\\System32;C:\\Program Files\\Salesforce CLI\\bin;${env.PATH}"
    }

    stage('Checkout') {
    steps {
        script {
            // Checkout the source code as configured in Jenkins job
            checkout scm
        }
    }
}

        stage('Process Branch') {
    steps {
        script {
            if (env.CHANGE_ID) {
                echo "Pull Request detected: PR-${env.CHANGE_ID}"
                currentBuild.description = "Pull Request Validation for ${env.CHANGE_BRANCH} -> ${env.CHANGE_TARGET}"
            } else if (env.BRANCH_NAME.startsWith('feature/')) {
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

        stage('Authorize Salesforce CLI') {
            steps {
                script {
                    // Authorizing using JWT
                    bat """
                    echo Authorizing Salesforce CLI...
                    sfdx auth:jwt:grant --clientid "${CLIENT_ID}" --jwtkeyfile "${JWT_KEY_FILE}" --username "${SFDC_USERNAME}" --instanceurl "${SFDC_INSTANCE_URL}" --setdefaultusername
                    """
                }
            }
        }

        stage('Validate Changes') {
            steps {
                script {
                    // Validate changes with dry-run for develop or feature branch
                    bat """
                    echo Validating branch ${env.BRANCH_NAME}...
                    sfdx project deploy start --target-org "${SFDC_USERNAME}" --wait 10 --dry-run --verbose
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
                    sfdx project deploy start --target-org "${SFDC_USERNAME}" --wait 10 --verbose
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
