pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials')  // Đảm bảo đây là ID đúng của Docker Hub credentials
        GITHUB_CREDENTIALS = credentials('github-access-token')  // Đảm bảo đây là ID đúng của GitHub credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clone mã nguồn từ GitHub
                git credentialsId: 'github-access-token', url: 'https://github.com/ADN2609/jenkins-git.git', branch: 'main'
            }
        }

        stage('Pull Backend Docker Image') {
            steps {
                script {
                    // Pull Docker image từ Docker Hub thay vì build lại
                    docker.image('doanh269/hackathon-backend:latest').pull()
                }
            }
        }

        stage('Pull Frontend Docker Image') {
            steps {
                script {
                    // Pull Docker image từ Docker Hub thay vì build lại
                    docker.image('doanh269/hackathon-frontend:latest').pull()
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    // Push Docker images lên Docker Hub
                    echo "Docker Credentials: ${DOCKER_CREDENTIALS}"
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
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
