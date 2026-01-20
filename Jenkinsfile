pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/gayas5/myapp-ci-pipeline.git',
                    credentialsId: 'github-creds' // remove if repo is public
            }
        }

        stage('Build') {
            steps {
                echo "Build stage running..."
            }
        }
    }
}
