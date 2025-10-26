pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/praveenshrivas/terraform-jenkins-demo.git'
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform Apply') {
            steps {
                sh 'terraform apply -auto-approve | tee tf-output.log'
            }
        }

        stage('Create Artifact') {
            steps {
                sh 'zip terraform-artifacts.zip main.tf tf-output.log'
                archiveArtifacts artifacts: 'terraform-artifacts.zip', fingerprint: true
            }
        }
    }

    post {
        always {
            echo "Cleaning up Terraform resources..."
            sh 'terraform destroy -auto-approve || true'
        }
    }
}
