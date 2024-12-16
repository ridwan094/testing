pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'rainover922/myapp:latest'
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning repository...'
                git 'https://github.com/ridwan094/testing'
                echo 'Repository successfully cloned!'
            }
        }
        stage('Docker Login') {
            steps {
                echo 'Logging in to Docker Hub...'
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u rainover922 --password-stdin
                    '''
                }
            }
        }
        stage('Build Image') {
            steps {
                echo 'Building Docker image...'
                sh '''
                docker build -t $DOCKER_IMAGE .
                '''
                echo 'Docker image built successfully!'
            }
        }
    }
    post {
        success {
            echo 'Build successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
