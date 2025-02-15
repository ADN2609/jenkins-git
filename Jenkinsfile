pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials')  //  ID của Docker Hub credentials
        GITHUB_CREDENTIALS = credentials('github-access-token')  //  ID của GitHub credentials
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
                    echo "Using Docker credentials for pull: ${DOCKER_CREDENTIALS}"

                    docker.image('doanh269/hackathon-backend:latest').pull()
                }
            }
        }

        stage('Pull Frontend Docker Image') {
            steps {
                script {
                    echo "Using Docker credentials for pull: ${DOCKER_CREDENTIALS}"

                    docker.image('doanh269/hackathon-frontend:latest').pull()
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    echo "Logging in to Docker Hub using credentials..."

                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        echo "Pushing backend image to Docker Hub..."
                        docker.image('doanh269/hackathon-backend:latest').push()

                        echo "Pushing frontend image to Docker Hub..."
                        docker.image('doanh269/hackathon-frontend:latest').push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
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
