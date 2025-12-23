pipeline {
    agent any

    environment {
        TF_IN_AUTOMATION = 'true'
        TF_CLI_ARGS      = '-no-color'

        // SSH credential (already created)
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
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                         credentialsId: 'aws-credentials']
                    ]) {
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
        }

        stage('Terraform Plan') {
            steps {
                dir('infra/terraform') {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                         credentialsId: 'aws-credentials']
                    ]) {
                        sh """
                            terraform plan \
                            -var-file=${BRANCH_NAME}.tfvars \
                            -out=tfplan
                        """
                    }
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
