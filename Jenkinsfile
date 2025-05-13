pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'
        DOCKERHUB_USERNAME = 'datnewbie'
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

        stage('Build and Push All Services') {
            steps {
                script {
                    def services = [
                        'spring-petclinic-vets-service',
                        'spring-petclinic-visits-service',
                        'spring-petclinic-customers-service',
                        'spring-petclinic-api-gateway',
                        'spring-petclinic-discovery-server',
                        'spring-petclinic-config-server'
                    ]

                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        for (service in services) {
                            echo "📦 Building service: ${service}"

                            def artifactName = service
                            def jarPath = "${service}/target/${artifactName}.jar"

                            // Build .jar
                            sh "./mvnw -pl ${service} clean package -DskipTests"

                            // Copy .jar vào docker/
                            sh "cp ${jarPath} docker/${artifactName}.jar"

                            // Build Docker image
                            def dockerImage = docker.build("${DOCKERHUB_USERNAME}/${artifactName}:${COMMIT_ID}", "--build-arg ARTIFACT_NAME=${artifactName} docker/")

                            // Push image
                            dockerImage.push()
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ All images built and pushed successfully with tag ${COMMIT_ID}."
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}

