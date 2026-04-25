pipeline {
    agent any

    environment {
        IMAGE_NAME = 'zoople-devops-workshop-vijin:latest'
        CONTAINER_NAME = 'vijin-app'
        APP_PORT = '3000'
        NGINX_DIR = '/home/ubuntu/nginx'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true

                docker network inspect nginx-network >/dev/null 2>&1 || docker network create nginx-network

                docker run -d \
                  --name $CONTAINER_NAME \
                  --network nginx-network \
                  -p $APP_PORT:$APP_PORT \
                  --restart unless-stopped \
                  $IMAGE_NAME
                '''
            }
        }

        stage('Setup Nginx') {
            steps {
                sh '''
                cd $NGINX_DIR
                docker compose up -d
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully'
        }

        failure {
            echo '❌ Deployment failed'
        }
    }
}