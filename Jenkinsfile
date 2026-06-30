pipeline {
    agent any

    environment {
        IMAGE_NAME = "demo-app:latest"
        CONTAINER_NAME = "demo-app"
    }

    stages {

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
                    sleep 15
                    docker exec $CONTAINER_NAME node -e "require('http').get('http://127.0.0.1:3000', res => process.exit(res.statusCode === 200 ? 0 : 1)).on('error', () => process.exit(1))" || (docker logs $CONTAINER_NAME && exit 1)
                '''
            }
        }
    }
}
