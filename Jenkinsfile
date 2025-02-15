pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials')  // ID của Docker Hub credentials
        GITHUB_CREDENTIALS = credentials('github-access-token')  // ID của GitHub credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'github-access-token', url: 'https://github.com/ADN2609/jenkins-git.git', branch: 'main'
            }
        }

        stage('Pull Backend Docker Image') {
            steps {
                script {
                    echo "Đang sử dụng Docker credentials để pull"
                    docker.image('doanh269/hackathon-backend:latest').pull()
                }
            }
        }

        stage('Pull Frontend Docker Image') {
            steps {
                script {
                    echo "Đang sử dụng Docker credentials để pull"
                    docker.image('doanh269/hackathon-frontend:latest').pull()
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    // Đăng nhập Docker Hub với credentials
                    echo "Đang đăng nhập vào Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    }
                    docker.image('doanh269/hackathon-backend:latest').push()
                    docker.image('doanh269/hackathon-frontend:latest').push()
                }
            }
        }

       stage('Deploy to Kubernetes') {
    steps {
        script {
            // Cung cấp kubeconfig từ Jenkins credentials
            withCredentials([file(credentialsId: 'kubeconfig-file', variable: 'KUBECONFIG')]) {
                sh 'export KUBECONFIG=$KUBECONFIG'
                sh 'kubectl config view'  // Kiểm tra thông tin kubeconfig
                sh 'kubectl apply -f backend-deployment.yaml'
                sh 'kubectl apply -f frontend-deployment.yaml'
            }
        }
    }
}

    post {
        always {
            cleanWs()
        }
    }
}
