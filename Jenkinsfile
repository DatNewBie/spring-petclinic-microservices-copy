pipeline {
    agent any

    environment {
        // Docker Hub credentials ID đã được lưu trong Jenkins credentials
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'
        DOCKERHUB_USERNAME = 'datnewbie'
        IMAGE_NAME = 'devops_project02'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Get Commit ID') {
            steps {
                script {
                    COMMIT_ID = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    echo "Commit ID: ${COMMIT_ID}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${COMMIT_ID}", "docker/")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        failure {
            echo 'Build failed!'
        }
        success {
            echo "Image ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${COMMIT_ID} pushed to Docker Hub successfully."
        }
    }
}
