pipeline {
    agent any

    environment {
        IMAGE_NAME = "akshithapulaboyina/node-k8s-app"
    }

    stages {

        // ✅ Checkout code from GitHub
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // ✅ Install Node.js dependencies
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        // ✅ Build Docker Image with BUILD_NUMBER
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        // ✅ Push Docker Image to DockerHub
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE_NAME:$BUILD_NUMBER
                    '''
                }
            }
        }

        // ✅ Stop and Remove Old Container
        stage('Stop Old Container') {
            steps {
                sh '''
                docker stop node-container || true
                docker rm node-container || true
                '''
            }
        }

        // ✅ Run New Container
        stage('Run New Container') {
            steps {
                sh '''
                docker run -d -p 3000:8080 \
                --name node-container \
                $IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful! Container is running."
        }
        failure {
            echo "❌ Deployment Failed! Check logs."
        }
    }
}
