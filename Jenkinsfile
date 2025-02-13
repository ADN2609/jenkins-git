pipeline {
    agent any

    environment {
        // Sử dụng Docker Hub credentials từ Jenkins
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials')  // ID Docker Hub credentials
        GITHUB_CREDENTIALS = credentials('github-access-token')  // ID GitHub credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Lấy mã nguồn từ GitHub
                git credentialsId: 'my-github-token', url: 'https://github.com/ADN2609/jenkins-git.git', branch: 'main'
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    // Build Docker image cho backend từ Dockerfile.backend
                    docker.build('doanh269/hackathon-backend:latest', '-f Dockerfile.backend .')
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    // Build Docker image cho frontend từ Dockerfile.frontend
                    docker.build('doanh269/hackathon-frontend:latest', '-f Dockerfile.frontend .')
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    // Đẩy Docker images lên Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        // Đẩy Docker images cho backend và frontend
                        docker.image('doanh269/hackathon-backend:latest').push()
                        docker.image('doanh269/hackathon-frontend:latest').push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Áp dụng Kubernetes Deployment và Service
                    sh 'kubectl apply -f backend-deployment.yaml'
                    sh 'kubectl apply -f frontend-deployment.yaml'
                }
            }
        }
    }

    post {
        always {
            // Dọn dẹp workspace sau khi build
            cleanWs()
        }
    }
}

