pipeline {
    agent any

    environment {
        TF_IN_AUTOMATION    = 'true'
        TF_CLI_ARGS         = '-no-color'
        AWS_DEFAULT_REGION  = 'us-east-1'
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
           TERRAFORM APPLY (TASK 1)
        ========================== */
        stage('Terraform Apply') {
            steps {
                dir('infra/terraform') {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                         credentialsId: 'aws-credentials']
                    ]) {
                        sh '''
                            terraform init
                            terraform apply -auto-approve -var-file=terraform.tfvars
                        '''
                    }
                }
            }
        }

        /* =========================
           CAPTURE OUTPUTS (TASK 1) ✅ FIXED
        ========================== */
        stage('Capture Terraform Outputs') {
            steps {
                dir('infra/terraform') {
                    script {
                        env.INSTANCE_IP = sh(
                            script: "terraform output -raw app_server_public_ip",
                            returnStdout: true
                        ).trim()

                        env.INSTANCE_ID = sh(
                            script: "terraform output -raw app_server_instance_id",
                            returnStdout: true
                        ).trim()

                        echo "EC2 Public IP: ${env.INSTANCE_IP}"
                        echo "EC2 Instance ID: ${env.INSTANCE_ID}"
                    }
                }
            }
        }

        /* =========================
           DYNAMIC INVENTORY (TASK 2)
        ========================== */
        stage('Create Dynamic Inventory') {
            steps {
                sh '''
                    echo "[splunk]" > dynamic_inventory.ini
                    echo "${INSTANCE_IP}" >> dynamic_inventory.ini
                    cat dynamic_inventory.ini
                '''
            }
        }

        /* =========================
           AWS HEALTH CHECK (TASK 3)
        ========================== */
        stage('Wait for EC2 Health') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-credentials']
                ]) {
                    sh '''
                        aws ec2 wait instance-status-ok \
                          --instance-ids ${INSTANCE_ID}
                    '''
                }
            }
        }

        /* =========================
           SPLUNK INSTALL (TASK 4)
        ========================== */
        stage('Install Splunk') {
            steps {
                ansiblePlaybook(
                    playbook: 'infra/ansible/splunk.yml',
                    inventory: 'dynamic_inventory.ini',
                    credentialsId: 'ec2-ssh-key',
                    extras: '--ssh-extra-args="-o StrictHostKeyChecking=no"'
                )
            }
        }

        /* =========================
           SPLUNK TEST (TASK 4)
        ========================== */
        stage('Test Splunk') {
            steps {
                ansiblePlaybook(
                    playbook: 'infra/ansible/test-splunk.yml',
                    inventory: 'dynamic_inventory.ini',
                    credentialsId: 'ec2-ssh-key',
                    extras: '--ssh-extra-args="-o StrictHostKeyChecking=no"'
                )
            }
        }

        /* =========================
           VALIDATE DESTROY (TASK 5)
        ========================== */
        stage('Validate Destroy') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    input message: 'Approve Terraform DESTROY?'
                }
            }
        }

        /* =========================
           TERRAFORM DESTROY (TASK 5)
        ========================== */
        stage('Terraform Destroy') {
            steps {
                dir('infra/terraform') {
                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                         credentialsId: 'aws-credentials']
                    ]) {
                        sh 'terraform destroy -auto-approve'
                    }
                }
            }
        }
    }

    /* =========================
       POST ACTIONS (TASK 5)
    ========================== */
    post {
        always {
            sh 'rm -f dynamic_inventory.ini'
        }
        failure {
            dir('infra/terraform') {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-credentials']
                ]) {
                    sh 'terraform destroy -auto-approve || true'
                }
            }
        }
        aborted {
            dir('infra/terraform') {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-credentials']
                ]) {
                    sh 'terraform destroy -auto-approve || true'
                }
            }
        }
    }
}
