def dockerImage  // Declare globally to avoid memory leak warning

pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'tejasvini2805/tickets-booking-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'develop', url: 'https://github.com/tejasvini2805/tickets-booking-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image using the build number as tag
                    dockerImage = docker.build("${DOCKER_HUB_REPO}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Make sure 'dockerhub-credentials' contains Username=tejasvini2805 and Password=PAT
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push()       // Push with build number tag
                        dockerImage.push('latest') // Push latest tag
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Windows agent requires 'bat' instead of 'sh'
                bat 'kubectl apply -f k8s-deployment.yaml'
                bat 'kubectl apply -f k8s-service.yaml'
            }
        }
    }

    post {
        always {
            echo "Cleaning up local Docker images..."
            script {
                // Remove local image after push to save space
                bat "docker rmi ${DOCKER_HUB_REPO}:${BUILD_NUMBER}"
                bat "docker rmi ${DOCKER_HUB_REPO}:latest"
            }
        }
        failure {
            echo "Pipeline failed! Check logs."
        }
        success {
            echo "Pipeline completed successfully!"
        }
    }
}
