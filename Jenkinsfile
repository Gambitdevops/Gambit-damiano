pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = "123456789012"
        AWS_REGION = "us-east-1"
        ECR_REPO = "my-ecr-repo"
        DOCKER_IMAGE = "my-app"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/your-repo.git'
            }
        }
        
        stage('Build & Test') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'staging') {
                        sh 'mvn clean package'
                        sh 'mvn test'
                    } else {
                        echo "Skipping tests for ${env.BRANCH_NAME} branch"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE:${env.BRANCH_NAME} ."
            }
        }

        stage('Push to ECR') {
            when {
                branch 'main'
            }
            steps {
                sh """
                aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                docker tag $DOCKER_IMAGE:${env.BRANCH_NAME} $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:${env.BRANCH_NAME}
                docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:${env.BRANCH_NAME}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }
            steps {
                sh 'kubectl apply -f k8s-deployment.yaml'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline execution failed!'
        }
    }
}
