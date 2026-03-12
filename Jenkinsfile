pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ACCOUNT_ID = '318948072665'
        ECR_REPO = 'car-website'
        IMAGE_URI = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
        CLUSTER = 'car-website-cluster'
        SERVICE = 'car-website-task-service'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/Sreejathukkani/car-website-docker.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t car-website .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh 'docker tag car-website:latest $IMAGE_URI:latest'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh 'docker push $IMAGE_URI:latest'
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                aws ecs update-service \
                --cluster $CLUSTER \
                --service $SERVICE \
                --force-new-deployment
                '''
            }
        }

    }
}
