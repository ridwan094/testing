pipeline {
    agent any
    tools {
        git 'Git'
    }
    environment {
        DOCKER_IMAGE = 'rainover922/myapp:latest'
    }
    stages {
        stage('Clone Repository') {
            steps {
                checkout scm: [
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/ridwan094/testing',
                        credentialsId: 'github-credentials'
                    ]]
                ]
            }
        }

        // Stage 2: Build Docker Image
        stage('Build Image') {
            steps {
                sh '''
                echo "Building Docker image..."
                docker build -t $DOCKER_IMAGE .
                '''
            }
        }

        // Stage 3: Security Scan with Trivy
        stage('Security Scan') {
            steps {
                sh '''
                echo "Running Trivy Security Scan..."
                if ! command -v trivy &> /dev/null; then
                    sudo apt-get update
                    sudo apt-get install -y wget apt-transport-https gnupg lsb-release
                    wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
                    echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
                    sudo apt-get update
                    sudo apt-get install -y trivy
                fi
                trivy image --exit-code 0 --severity HIGH,CRITICAL $DOCKER_IMAGE
                '''
            }
        }

        // Stage 4: Push Image to Docker Hub
        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials', // Replace with DockerHub credential ID
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "Pushing Docker image to DockerHub..."
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        // Stage 5: Deploy to Kubernetes
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                echo "Deploying to Kubernetes..."
                if ! command -v kubectl &> /dev/null; then
                    echo "Kubectl not found, installing..."
                    curl -LO "https://dl.k8s.io/release/v1.24.0/bin/linux/amd64/kubectl"
                    chmod +x kubectl
                    sudo mv kubectl /usr/local/bin/
                fi
                kubectl apply -f k8s/Deployment.yaml
                kubectl rollout status deployment/myapp-deployment
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed.'
        }
    }
}
