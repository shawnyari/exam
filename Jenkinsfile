pipeline {
    agent {
        label 'docker'
    }

    environment {
        DOCKER_IMAGE = 'shawnyari/project'
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

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'yarishawn',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin

                        docker push $DOCKER_IMAGE:latest
                        docker logout
                    '''
                }
            }
        }
    }
}
