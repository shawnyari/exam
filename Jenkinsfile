pipeline {
    agent { label 'docker' }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t yarishawn/exam:latest .'
            }
        }

        stage('Test') {
            steps {
                sh '''
                docker rm -f exam-test || true
                docker run -d -p 5000:5000 --name exam-test yarishawn/exam:latest
                sleep 3
                curl http://localhost:5000
                docker rm -f exam-test
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push yarishawn/exam:latest
                    '''
                }
            }
        }
    }
}
