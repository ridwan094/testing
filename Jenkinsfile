pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'rainover922/myapp:latest' // Nama Docker Image
    }
    stages {
        // Stage 1: Clone Repository
        stage('Clone Repository') {
            steps {
                echo "Cloning repository..."
                checkout scm: [
                    $class: 'GitSCM',
                    branches: [[name: '*/main']], // Branch main
                    userRemoteConfigs: [[
                        url: 'https://github.com/ridwan094/testing',
                        credentialsId: 'github-credentials' // Ganti dengan ID kredensial Jenkins
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
                # Cek apakah Docker sudah tersedia
                if ! command -v docker &> /dev/null; then
                    echo "Docker not found. Exiting..."
                    exit 1
                fi
                
                # Build Docker Image
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
