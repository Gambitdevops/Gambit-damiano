pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '977098985978'
        AWS_REGION = 'us-east-1'
        ECR_REPO_NAME = 'gambit-damiano'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout Jenkinsfile Repo') {
            steps {
                echo "Jenkinsfile checked out from Gambit-damiano@devops"
                // Already checked out automatically
            }
        }

        stage('Clone Flask App Code') {
            steps {
                sh 'git clone https://github.com/Gambitdevops/flask-aws-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG} flask-aws-app
                    """
                }
            }
        }

        stage('Login to ECR') {
            steps {
                script {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    """
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh """
                        docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo "You can add SSH deploy commands here"
            }
        }
    }
}
