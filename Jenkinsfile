pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'pathfinding-visualizer'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm ci'
            }
        }

        stage('Lint') {
            steps {
                bat 'npm run lint'
            }
        }

        stage('Build') {
            steps {
                bat 'npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                    docker stop %DOCKER_IMAGE% || true
                    docker rm %DOCKER_IMAGE% || true
                    docker run -d -p 80:80 --name %DOCKER_IMAGE% %DOCKER_IMAGE%
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}