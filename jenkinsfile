pipeline {
    agent any
    environment {
        DOCKER_IMAGE = '<DockerHub-Username>/myapp:latest'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/ridwan094/testing'
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }
        stage('Security Scan') {
            steps {
                sh '''
                # Install Trivy if not already installed
                if ! command -v trivy &> /dev/null; then
                    sudo apt-get update
                    sudo apt-get install -y wget apt-transport-https gnupg lsb-release
                    wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
                    echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
                    sudo apt-get update
                    sudo apt-get install -y trivy
                fi

                # Run Trivy scan
                trivy image --exit-code 0 --severity HIGH,CRITICAL $DOCKER_IMAGE
                '''
            }
        }
        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_IMAGE
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
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
