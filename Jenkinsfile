pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-southeast-2'
        ECR_REGISTRY = '975630231376.dkr.ecr.ap-southeast-2.amazonaws.com'
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

        stage('Login ECR') {
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
                    --region $AWS_REGION \
                    | docker login \
                    --username AWS \
                    --password-stdin $ECR_REGISTRY
                    '''
                }
            }
        }

        stage('Verify ECR Access') {
            steps {
                sh '''
                aws ecr describe-repositories \
                --region $AWS_REGION
                '''
            }
        }
    }
}