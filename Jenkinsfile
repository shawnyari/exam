pipeline {
    agent { label 'docker' }

    environment {
        IMAGE_NAME = "yarishawn/exam:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Code checked out from GitHub'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Test Container') {
            steps {
                sh 'docker rm -f exam-container || true'
                sh 'docker run -d --name exam-container -p 5000:5000 $IMAGE_NAME'
                sh 'sleep 5'
                sh 'curl http://localhost:5000'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }
    }
}

stage('Deploy to Kubernetes') {
    steps {
        sh '''
        ssh -o StrictHostKeyChecking=no ubuntu@172.31.21.73
        kubectl set image deployment/exam-app exam=yarishawn/exam:latest || kubectl create deployment exam-app --image=yarishawn/exam:latest
        kubectl expose deployment exam-app --type=NodePort --port=5000 --target-port=5000 || true
        kubectl rollout status deployment/exam-app
        kubectl get pods
        kubectl get svc
        "
        '''
    }
}
