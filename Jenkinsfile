pipeline {
    agent any

    environment {
        IMAGE_NAME = "demo-app:latest"
        CONTAINER_NAME = "demo-app"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Repository checked out by Jenkins'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Stop Existing Container') {
            steps {
                sh '''
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker run -d \
                        --name $CONTAINER_NAME \
                        -p 3000:3000 \
                        $IMAGE_NAME
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    sleep 5

                    STATUS=$(curl -o /dev/null -s -w "%{http_code}" http://localhost:3000)

                    if [ "$STATUS" != "200" ]; then
                        echo "Health check failed"
                        exit 1
                    fi
                '''
            }
        }

        stage('Finished') {
            steps {
                echo 'Deployment successful!'
            }
        }
    }
}