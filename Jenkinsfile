pipeline {
    agent any
    environment {
        PATH = "/usr/bin:/usr/local/bin:${env.PATH}"
        DOCKER_IMAGE = 'rainover922/myapp:latest'
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

        // Stage 2: Build Docker Image
        stage('Build Image') {
            steps {
                echo "Building Docker image..."
                sh '''
                docker --version
                docker build -t $DOCKER_IMAGE .
                '''
                echo "Docker image successfully built!"
            }
        }
    }

    post {
        success {
            echo 'Stages 1 and 2 completed successfully!'
        }
        failure {
            echo 'Build failed during Clone Repository or Build Image.'
        }
    }
}
