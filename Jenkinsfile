pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPOSITORY = 'terraform-web-app'
        AWS_ACCOUNT_ID = '299590373603'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        IMAGE_NAME = "${ECR_REGISTRY}/${ECR_REPOSITORY}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --watchAll=false'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }

        stage('ECR Login') {
            steps {
                sh '''
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login --username AWS --password-stdin ${ECR_REGISTRY}
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push ${IMAGE_NAME}:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Docker image pushed successfully to ECR.'
                echo 'Next step: deploy the image to EC2.'
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed!'
        }
    }
}