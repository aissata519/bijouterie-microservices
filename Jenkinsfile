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
        stage('Update manifests') {
            steps {
                // 1. Remplace l'ancien tag par le nouveau tag  dans les déploiements(ex v3->v4)
                powershell """
                    Get-ChildItem -Path k8s/*-deployment.yaml | ForEach-Object {
                        (Get-Content \$_.FullName) -replace '(${DOCKER_USER}/bijouterie-[a-z]+:)[a-zA-Z0-9._-]+', '\$1${IMAGE_TAG}' | Set-Content \$_.FullName
                    }
                """
                // 2. Commit et Push automatique vers GitHub
                bat '''
                    git config user.email "jenkins@bijouterie.local"
                    git config user.name "Jenkins CI"
                    git add k8s/*-deployment.yaml
                    git commit -m "chore(gitops): update image tags to %IMAGE_TAG%"
                    git push origin HEAD:main
                '''
                
            }
        }
    }
}
