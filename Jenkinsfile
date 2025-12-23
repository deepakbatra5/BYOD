pipeline {
    agent any

    environment {
        TF_IN_AUTOMATION = 'true'
        TF_CLI_ARGS      = '-no-color'
        SSH_CRED_ID      = 'ec2-ssh-key'
    }

    stages {

        /* =========================
           CHECKOUT
        ========================== */
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        /* =========================
           DEBUG (VERY IMPORTANT)
        ========================== */
        stage('Debug Info') {
            steps {
                echo "BRANCH_NAME from Jenkins: ${env.BRANCH_NAME}"
                sh '''
                    echo "Git branch detected:"
                    git branch --show-current
                '''
            }
        }

        /* =========================
           TERRAFORM INIT
        ========================== */
        stage('Terraform Init') {
            steps {
                dir('infra/terraform') {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                         credentialsId: 'aws-credentials']
                    ]) {
                        sh '''
                            terraform init
                            echo "Using terraform.tfvars"
                            ls -l
                        '''
                    }
                }
            }
        }

        /* =========================
           TERRAFORM PLAN (ALL CASES)
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
           MANUAL APPROVAL (NEVER SKIPPED)
        ========================== */
        stage('Manual Approval') {
            steps {
                script {
                    def userChoice = input(
                        message: "Approve Terraform APPLY?",
                        ok: "Approve",
                        parameters: [
                            choice(
                                name: 'ACTION',
                                choices: ['NO', 'YES'],
                                description: 'Select YES to apply Terraform changes'
                            )
                        ]
                    )

                    if (userChoice == 'YES') {
                        env.DO_APPLY = 'true'
                    } else {
                        env.DO_APPLY = 'false'
                    }
                }
            }
        }

        /* =========================
           TERRAFORM APPLY (CONTROLLED)
        ========================== */
        stage('Terraform Apply') {
            steps {
                script {
                    if (env.DO_APPLY == 'true') {
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
                    } else {
                        echo "Terraform Apply was NOT approved. Skipping apply safely."
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Terraform pipeline completed successfully."
        }
        aborted {
            echo "Pipeline aborted by user."
        }
        failure {
            echo "Terraform pipeline failed."
        }
    }
}
