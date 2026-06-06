pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REGISTRY = '<account-id>.dkr.ecr.ap-south-1.amazonaws.com'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/<user>/StreamingApp.git'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'docker build -t frontend ./frontend'
            }
        }

        stage('Build Backend') {
            steps {
                sh 'docker build -t backend ./backend'
            }
        }

        stage('Login ECR') {
            steps {
                sh '''
                aws ecr get-login-password \
                --region $AWS_REGION \
                | docker login \
                --username AWS \
                --password-stdin $ECR_REGISTRY
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker tag frontend $ECR_REGISTRY/streaming-frontend:latest
                docker tag backend $ECR_REGISTRY/streaming-backend:latest

                docker push $ECR_REGISTRY/streaming-frontend:latest
                docker push $ECR_REGISTRY/streaming-backend:latest
                '''
            }
        }
    }
}