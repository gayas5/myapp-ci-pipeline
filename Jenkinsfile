pipeline {
    agent any

    environment {
        IMAGE_NAME = "gayas555/myapp"
        TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build App') {
            steps {
                echo "Building application..."
            }
        }

        stage('Run Tests') {
            steps {
                echo "Running tests..."
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    set -e
                    docker --version
                    docker build -t ${IMAGE_NAME}:${TAG} .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:${TAG}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "CI/CD Pipeline completed successfully"
        }
        failure {
            echo "CI/CD Pipeline failed"
        }
    }
}
