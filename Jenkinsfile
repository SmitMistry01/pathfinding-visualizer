pipeline {
    agent any

    environment {
        // Update these variables according to your setup
        DOCKER_REGISTRY = 'docker.io'  // or your private registry
        DOCKER_IMAGE = 'pathfinding-visualizer'
        DOCKER_CREDENTIALS = 'docker-credentials'
        VERSION = "${BUILD_NUMBER}"
        // Windows-specific path configurations
        NODE_HOME = tool 'NodeJS' // Name of your NodeJS installation in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Use bat for Windows commands
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

        stage('Build Docker Image') {
            steps {
                script {
                    // Windows-style Docker commands
                    bat "docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${VERSION} ."
                    bat "docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    bat "docker login ${DOCKER_REGISTRY} -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%"
                    bat "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${VERSION}"
                    bat "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Windows-style docker-compose commands
                    bat 'docker-compose down || exit 0'
                    bat 'docker-compose up -d'
                }
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