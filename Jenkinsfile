pipeline {
    agent any

    environment {
        // Replace with your Docker Hub username
        IMAGE_NAME = "gayas5/myapp"
        TAG = "latest"
    }

    stages {

        // 1️⃣ Checkout Code from GitHub
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/gayas5/myapp-ci-pipeline.git',
                    credentialsId: 'github-creds' // remove if repo is public
            }
        }

        // 2️⃣ Build Application
        stage('Build App') {
            steps {
                echo "Building application..."
                // Replace with actual build commands if needed, e.g.,
                // sh 'mvn clean install' or 'npm install'
            }
        }

        // 3️⃣ Run Tests
        stage('Run Tests') {
            steps {
                echo "Running tests..."
                // Replace with actual test commands, e.g.,
                // sh 'mvn test' or 'npm test'
            }
        }

        // 4️⃣ Build Docker Image
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image $IMAGE_NAME:$TAG"
                    sh "docker build -t $IMAGE_NAME:$TAG ."
                }
            }
        }

        // 5️⃣ Push Docker Image to Docker Hub
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds', // Docker Hub credentials ID in Jenkins
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $IMAGE_NAME:$TAG
                        '''
                    }
                }
            }
        }

        // 6️⃣ Optional: Deploy via ArgoCD
        stage('Deploy via ArgoCD') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'argocd-creds', // ArgoCD credentials ID in Jenkins
                    usernameVariable: 'ARGO_USER',
                    passwordVariable: 'ARGO_PASS'
                )]) {
                    script {
                        sh '''
                        argocd login <argocd-server> --username $ARGO_USER --password $ARGO_PASS --insecure
                        argocd app sync <argocd-app-name>
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo " CI/CD Pipeline completed successfully"
        }
        failure {
            echo " CI/CD Pipeline failed"
        }
    }
}
