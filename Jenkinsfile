pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'rainover922/myapp:latest' // Nama Docker image
    }
    stages {
        // Stage 1: Clone Repository
        stage('Clone Repository') {
            steps {
                echo "Cloning repository..."
                checkout scm: [
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/ridwan094/testing',
                        credentialsId: 'github-credentials'
                    ]]
                ]
                echo "Repository successfully cloned!"
            }
        }

        // Stage 2: Docker Login
        stage('Docker Login') {
            steps {
                echo "Logging in to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
                echo "Docker login successful!"
            }
        }

        // Stage 3: Build Docker Image
        stage('Build Image') {
            steps {
                echo "Building Docker image..."
                sh '''
                docker build -t $DOCKER_IMAGE .
                '''
                echo "Docker image built successfully!"
            }
        }

        // Stage 4: Push Docker Image
        stage('Push Image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    docker push $DOCKER_IMAGE
                    '''
                }
                echo "Docker image pushed successfully!"
            }
        }
    }

    post {
        success {
            echo 'Build, Docker Login, and Push completed successfully!'
        }
        failure {
            echo 'Build or Docker operation failed.'
        }
    }
}
