pipeline {
    agent any

    environment {
        TF_IN_AUTOMATION = 'true'
        TF_CLI_ARGS      = '-no-color'
        SSH_CRED_ID      = 'ec2-ssh-key'
    }

    stages {

        /* =========================
           DEBUG (IMPORTANT)
        ========================== */
        stage('Debug Branch') {
            steps {
                echo "Running on branch: ${env.BRANCH_NAME}"
                sh '''
                    echo "Git branch from repo:"
                    git branch --show-current
                '''
            }
        }

        /* =========================
           CHECKOUT
        ========================== */
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        /* =========================
           TERRAFORM INIT
        ========================== */
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
                            echo "Using variable file: terraform.tfvars"
                            echo "================================="

                            ls -l
                            cat terraform.tfvars
                        '''
                    }
                }
            }
        }

        /* =========================
           TERRAFORM PLAN (ALL BRANCHES)
        ========================== */
        stage('Terraform Plan') {
            steps {
                dir('infra/terraform') {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                         credentialsId: 'aws-credentials']
                    ]) {
                        sh '''
                            terraform plan \
                              -var-file=terraform.tfvars \
                              -out=tfplan
                        '''
                    }
                }
            }
        }

        /* =========================
           MANUAL VALIDATION (DEV ONLY)
        ========================== */
        stage('Validate Apply') {
            when {
                expression {
                    env.BRANCH_NAME == 'dev'
                }
            }
            steps {
                input message: 'Approve Terraform Apply for DEV environment?'
            }
        }

        /* =========================
           TERRAFORM APPLY (DEV ONLY)
        ========================== */
        stage('Terraform Apply') {
            when {
                expression {
                    env.BRANCH_NAME == 'dev'
                }
            }
            steps {
                dir('infra/terraform') {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                         credentialsId: 'aws-credentials']
                    ]) {
                        sh '''
                            terraform apply -auto-approve tfplan
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Terraform pipeline completed successfully."
        }
        failure {
            echo "Terraform pipeline failed."
        }
    }
}
