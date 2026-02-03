pipeline {
    agent any

    environment {
        IMAGE_NAME = "python-ci-app"
        CONTAINER_NAME = "python-ci-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/vpsubhash1302/jenkins-docker-pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Run Tests Inside Docker') {
            steps {
                sh '''
                docker run --rm $IMAGE_NAME pytest
                '''
            }
        }

        stage('Run Application Container') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker run -d --name $CONTAINER_NAME $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD Pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}
