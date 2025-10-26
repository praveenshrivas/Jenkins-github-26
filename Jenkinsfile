pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
    }

    stages {
        stage('Pull Code from SCM') {
            steps {
                echo "Cloning repository from GitHub..."
                // Since Jenkinsfile is already in the repo, it auto-checks out.
                // This stage is just to make it visible in Blue Ocean.
                sh 'ls -l'
            }
        }

        stage('Terraform Init') {
            steps {
                echo "Initializing Terraform..."
                sh 'terraform init'
            }
        }

        stage('Terraform Plan') {
            steps {
                echo "Planning Terraform changes..."
                sh 'terraform plan -out=tfplan | tee tf-plan.log'
            }
        }

        stage('Terraform Apply') {
            steps {
                echo "Applying Terraform plan to create resources..."
                sh 'terraform apply -auto-approve | tee tf-output.log'
            }
        }

        stage('Create and Archive Artifacts') {
            steps {
                echo "Creating artifact and archiving..."
                sh 'zip terraform-artifacts.zip main.tf tf-output.log tf-plan.log'
                archiveArtifacts artifacts: 'terraform-artifacts.zip', fingerprint: true
            }
        }
    }

    post {
        always {
            echo "Cleaning up Terraform resources..."
            sh '''
            set +e
            terraform destroy -auto-approve
            exit 0
            '''
        }
    }
}
