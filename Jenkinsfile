pipeline {
    agent any
    environment {
        IMAGE_NAME = 'sample-node-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
        NEXUS_REPO = 'http://4.240.99.44:8081/repository/docker-hosted'
        DOCKER_CREDENTIALS_ID = 'nexus-cred'
    }

    stages {
        stage('Clone Repository') {
            steps {
               git branch: 'main', url: 'https://github.com/Syed-894/Sample_node_app.git'
            }
        }

        stage('Build Docker Image') {       
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push to Nexus') {
            steps {
                script {
                    docker.withRegistry("${NEXUS_REPO}", "${DOCKER_CREDENTIALS_ID}") {
                         dockerImage.push("${IMAGE_TAG}")
                         dockerImage.push("latest")
                    }
                }
            }
        }
    }

    post {
        success {
            slackSend (
                channel: '#new-channel',
                message: "✅ Build and push successfull for ${IMAGE_NAME}:${IMAGE_TAG}",
                color: 'good'
            )
        }
        failure {
            slackSend (
                channel: '#new-channel',
                message: "❌ Build failed for ${IMAGE_NAME}:${IMAGE_TAG}",
                color: 'danger'
            )
        }
    }
}
