pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-southeast-2'
        AWS_ACCOUNT_ID = '975630231376'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }

    stages {

        stage('Verify Docker') {
            steps {
                sh 'docker --version'
            }
        }

        stage('Verify AWS CLI') {
            steps {
                sh 'aws --version'
            }
        }

        stage('Build Frontend') {
            steps {
                sh '''
                docker build \
                -t streamingapp-frontend:latest \
                ./frontend
                '''
            }
        }

        stage('Build Auth') {
            steps {
                sh '''
                docker build \
                -t streamingapp-auth:latest \
                ./backend/authService
                '''
            }
        }

        stage('Build Streaming') {
            steps {
                sh '''
                docker build \
                -t streamingapp-streaming:latest \
                -f backend/streamingService/Dockerfile \
                ./backend
                '''
            }
        }

        stage('Build Admin') {
            steps {
                sh '''
                docker build \
                -t streamingapp-admin:latest \
                -f backend/adminService/Dockerfile \
                ./backend
                '''
            }
        }

        stage('Build Chat') {
            steps {
                sh '''
                docker build \
                -t streamingapp-chat:latest \
                -f backend/chatService/Dockerfile \
                ./backend
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'aws-ecr-creds',
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                    )
                ]) {
                    sh '''
                    aws ecr get-login-password \
                    --region $AWS_REGION | \
                    docker login \
                    --username AWS \
                    --password-stdin $ECR_REGISTRY
                    '''
                }
            }
        }

        stage('Tag Images') {
            steps {
                sh '''
                docker tag streamingapp-frontend:latest $ECR_REGISTRY/streamingapp-frontend:latest
                docker tag streamingapp-auth:latest $ECR_REGISTRY/streamingapp-auth:latest
                docker tag streamingapp-streaming:latest $ECR_REGISTRY/streamingapp-streaming:latest
                docker tag streamingapp-admin:latest $ECR_REGISTRY/streamingapp-admin:latest
                docker tag streamingapp-chat:latest $ECR_REGISTRY/streamingapp-chat:latest
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker push $ECR_REGISTRY/streamingapp-frontend:latest
                docker push $ECR_REGISTRY/streamingapp-auth:latest
                docker push $ECR_REGISTRY/streamingapp-streaming:latest
                docker push $ECR_REGISTRY/streamingapp-admin:latest
                docker push $ECR_REGISTRY/streamingapp-chat:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: All images pushed to Amazon ECR'
        }

        failure {
            echo 'FAILED: Check Jenkins console output'
        }
    }
}