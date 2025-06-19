pipeline {
    agent any

    environment {
        IMAGE_NAME = 'galeri-web'
        CONTAINER_NAME = 'galeri-web'
        PORT = '8081'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/chrisn41/final-destination-12.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Deploy Docker Container') {
            steps {
                sh '''
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true
                docker run -d --name $CONTAINER_NAME -p $PORT:80 $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Galeri berhasil dideploy ke Nginx!"
        }
        failure {
            echo "❌ Pipeline gagal. Cek error log."
        }
    }
}
