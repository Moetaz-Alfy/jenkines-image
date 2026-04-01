pipeline {
    agent any

    environment {
        IMAGE_NAME      = 'motazelalfy/hello-flask'
        IMAGE_TAG       = "${env.BUILD_NUMBER}"
        REGISTRY        = 'docker.io'
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'   // Jenkins credential ID
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://${REGISTRY}", "${DOCKER_CREDENTIALS_ID}") {
                        def img = docker.image("${IMAGE_NAME}:${IMAGE_TAG}")
                        img.push()
                        // Also tag & push as "latest"
                        img.push('latest')
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Image ${IMAGE_NAME}:${IMAGE_TAG} built and pushed successfully."
        }
        failure {
            echo "❌ Pipeline failed. Check logs above."
        }
        always {
            // Remove the local image to keep the agent disk clean
            sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true"
            sh "docker rmi ${IMAGE_NAME}:latest    || true"
        }
    }
}
