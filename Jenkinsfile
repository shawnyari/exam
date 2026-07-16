pipeline {
    agent {
        label 'docker'
    }

    environment {
        DOCKER_IMAGE = 'shawnyari/exam'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Test Docker Image') {
            steps {
                sh 'docker run --rm $DOCKER_IMAGE:latest python --version'
            }
        }
    }
}
