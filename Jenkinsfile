pipeline {
    agent { label 'docker' }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t exam-app .'
            }
        }

        stage('Run Container Test') {
            steps {
                sh 'docker rm -f exam-container || true'
                sh 'docker run -d --name exam-container -p 5000:5000 exam-app'
                sh 'sleep 5'
                sh 'curl http://localhost:5000'
            }
        }
    }
}
