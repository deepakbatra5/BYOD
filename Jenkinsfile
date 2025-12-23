pipeline {
    agent any

    environment {
        TF_IN_AUTOMATION = 'true'
        TF_CLI_ARGS      = '-no-color'

        // AWS credentials (EXACT ID from Jenkins)
        AWS_ACCESS_KEY_ID     = credentials('aws-credentials').accessKey
        AWS_SECRET_ACCESS_KEY = credentials('aws-credentials').secretKey

        // SSH key credential ID (used later for Ansible/EC2)
        SSH_CRED_ID = 'ec2-ssh-key'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Init & Inspect Vars') {
            steps {
                dir('infra/terraform') {
                    sh '''
                      echo "Initializing Terraform..."
                      terraform init

                      echo "================================="
                      echo "Using variable file:"
                      echo "${BRANCH_NAME}.tfvars"
                      echo "================================="

                      cat ${BRANCH_NAME}.tfvars
                    '''
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir('infra/terraform') {
                    sh """
                      terraform plan \
                      -var-file=${BRANCH_NAME}.tfvars \
                      -out=tfplan
                    """
                }
            }
        }

        stage('Validate Apply') {
            when {
                branch 'dev'
            }
            steps {
                input message: 'Approve Terraform Apply for DEV environment?'
            }
        }
    }
}
