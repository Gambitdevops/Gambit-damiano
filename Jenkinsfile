pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "977098985978.dkr.ecr.us-east-1.amazonaws.com/gambit-damiano"
    }

    stages {
        stage('Checkout Jenkinsfile Repo') {
            steps {
                git branch: 'devops', url: 'https://github.com/Gambitdevops/gambit-damiano.git'
            }
        }

        stage('Clone App Repo') {
            steps {
                sh 'git clone https://github.com/Gambitdevops/flask-aws-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('flask-aws-app') {
                    sh 'docker build -t $ECR_REPO:latest .'
                }
            }
        }

        stage('Login to ECR') {
            steps {
                sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO'
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push $ECR_REPO:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo 'Add your deployment script here'
            }
        }
    }
}
