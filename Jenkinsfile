pipeline {
    agent { label 'bijouterie-node' }

    environment {
        DOCKER_USER = 'aissata519'
        IMAGE_TAG = "v${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Images') {
            steps {
                bat 'docker build -t bijouterie-frontend:local ./frontend'
                bat 'docker build -t bijouterie-backend:local ./backend'
                bat 'docker build -t bijouterie-mysql:local ./database'
            }
        }
        stage('Docker Tag') {
            steps {
                bat "docker tag bijouterie-frontend:local ${DOCKER_USER}/bijouterie-frontend:${IMAGE_TAG}"
                bat "docker tag bijouterie-backend:local ${DOCKER_USER}/bijouterie-backend:${IMAGE_TAG}"
                bat "docker tag bijouterie-mysql:local ${DOCKER_USER}/bijouterie-mysql:${IMAGE_TAG}"
            }
        }
        stage('Docker Push') {
            steps {
            
                bat "docker push ${DOCKER_USER}/bijouterie-frontend:${IMAGE_TAG}"
                bat "docker push ${DOCKER_USER}/bijouterie-backend:${IMAGE_TAG}"
                bat "docker push ${DOCKER_USER}/bijouterie-mysql:${IMAGE_TAG}"
                
            }
        }

        

}
