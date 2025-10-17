pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '171433609970'
        AWS_REGION = 'ap-south-1'
        ECR_REPO = 'making_pipeline'
        IMAGE_NAME = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo '📥 Cloning GitHub repository...'
                git branch: 'main', url: 'https://github.com/Anilbingishetti/Testing_Jenkins.git'
            }
        }

        stage('List Files') {
            steps {
                echo '📂 Listing files in workspace...'
                bat 'dir'
            }
        }

        stage('Compile Java Code') {
            steps {
                echo '⚙️ Compiling Java files...'
                bat 'javac Test.java'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                bat 'docker build -t java-test .'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                echo '🔐 Logging in to AWS ECR...'
                bat "aws ecr get-login-password --region %AWS_REGION% | docker login --username AWS --password-stdin %AWS_ACCOUNT_ID%.dkr.ecr.%AWS_REGION%.amazonaws.com"
            }
        }

        stage('Tag & Push Docker Image') {
            steps {
                echo '📤 Tagging and pushing Docker image to ECR...'
                bat "docker tag java-test %IMAGE_NAME%"
                bat "docker push %IMAGE_NAME%"
            }
        }

        stage('Run Java Program in Docker') {
            steps {
                echo '🚀 Running Java program inside Docker container...'
                bat "docker run --rm %IMAGE_NAME%"
            }
        }
    }

    post {
        always {
            echo '🧹 Cleaning up Docker...'
            bat 'docker system prune -f'
        }
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs for details.'
        }
    }
}
