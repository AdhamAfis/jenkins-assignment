pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE = "adhamafis/test-app"
        DOCKER_TAG = "latest"
    }
    
    options {
        skipDefaultCheckout(true)
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script {
                    // Build the image
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    
                    // Login to DockerHub
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    
                    // Push the image
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    
                    // Logout
                    sh 'docker logout'
                }
            }
        }
    }
} 