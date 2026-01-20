pipeline {
    agent any

    environment {
        IMAGE_NAME = "gayas555/myapp"
        TAG = "latest"
    }

    stages {

        stage('Build App') {
            steps {
                echo "Building application..."
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                docker run --rm \
                -v $(pwd):/app \
                -w /app \
                python:3.9-slim \
                sh -c "pip install -r requirements.txt && pytest tests/"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
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
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$TAG
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "CI Pipeline completed successfully"
        }
        failure {
            echo "CI Pipeline failed"
        }
    }
}
