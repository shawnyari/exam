pipeline {
    agent { label 'docker' }

    environment {
        IMAGE_NAME = "yarishawn/exam:latest"
        K8S_NODE = "54.175.19.157"
    }

    stages {
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

        stage('Deploy to Kubernetes') {
    steps {
        sh '''
        ssh -i /root/.ssh/id_ed25519 -o StrictHostKeyChecking=no ubuntu@54.175.19.157 "
        kubectl get nodes
        kubectl get pods
        kubectl get svc
        "
        '''
    }
}
