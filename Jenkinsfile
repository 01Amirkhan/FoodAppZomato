pipeline {

    agent any

    environment {
        AWS_ACCOUNT_ID = "195334031155"
        AWS_REGION = "ap-south-1"
        IMAGE_NAME = "zomato-app"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/01Amirkhan/FoodAppZomato.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                docker tag $IMAGE_NAME:latest \
                $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_NAME:latest
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin \
                $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''

                sh '''
                docker push \
                $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$IMAGE_NAME:latest
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
