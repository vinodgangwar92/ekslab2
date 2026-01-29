pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ECR_REPO = "762339788648.dkr.ecr.ap-south-1.amazonaws.com/static-eks-site"
        IMAGE_TAG = "95"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://gitlab.com/SOFTAPP-TECHNOLOGIES/jenkins-ci-cd-via-kubernete-eks.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %ECR_REPO%:%IMAGE_TAG% .'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withCredentials([
                  string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                  string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    bat '''
                    aws ecr get-login-password --region %AWS_REGION% ^
                    | docker login --username AWS --password-stdin %ECR_REPO%
                    '''
                }
            }
        }

        stage('Push Image to ECR') {
            steps {
                bat 'docker push %ECR_REPO%:%IMAGE_TAG%'
            }
        }

        stage('Deploy to EKS') {
            steps {
                bat '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}
