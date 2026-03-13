pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "318948072665.dkr.ecr.us-east-1.amazonaws.com/car-website"
        CLUSTER_NAME = "car-website-cluster"
        SERVICE_NAME = "car-website-task-service"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t car-website .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                bat 'docker tag car-website:latest %ECR_REPO%:latest'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                bat 'aws ecr get-login-password --region %AWS_REGION% | docker login --username AWS --password-stdin 318948072665.dkr.ecr.us-east-1.amazonaws.com'
            }
        }

        stage('Push Image to ECR') {
            steps {
                bat 'docker push %ECR_REPO%:latest'
            }
        }

        stage('Deploy to ECS') {
            steps {
                bat 'aws ecs update-service --cluster %CLUSTER_NAME% --service %SERVICE_NAME% --force-new-deployment'
            }
        }

    }
}
