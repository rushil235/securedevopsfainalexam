pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-personal-access-token')
        IMAGE_NAME = "healthcare-app"
        CONTAINER_NAME = "healthcare-container"
        APP_PORT = "3000"  // Exposing this on the host machine
    }

    stages {
        stage('Clone Repo') {
            steps {
                script {
                    echo "📥 Cloning the GitHub Repository..."
                    git branch: 'main', url: 'https://github.com/rushil235/securedevopsfainalexam.git'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "🐳 Building Docker Image..."
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Basic Security Compliance Check') {
            steps {
                script {
                    echo "🛡️ Checking for basic container security compliance..."
                    echo "Checking for root user in the container..."
                    sh "docker inspect ${IMAGE_NAME} | grep '\"User\": \"\"' && echo '⚠️ Container running as root!' || echo '✅ No root user found!'"
                }
            }
        }

        stage('Stop Old Container') {
            steps {
                script {
                    echo "🛑 Stopping and Removing Old Container..."
                    sh """
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                    """
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    echo "🚀 Running Docker Container..."
                    sh "docker run -d -p ${APP_PORT}:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit: http://<your-ec2-ip>:${APP_PORT}"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}
