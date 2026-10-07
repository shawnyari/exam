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

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ubuntu@34.201.21.117 '
                    kubectl create deployment exam-app \
                    --image=yarishawn/exam:latest \
                    --dry-run=client -o yaml | kubectl apply -f -

                    kubectl set image deployment/exam-app \
                    exam-app=yarishawn/exam:latest

                    kubectl expose deployment exam-app \
                    --type=NodePort \
                    --port=5000 \
                    --target-port=5000 \
                    --name=exam-service \
                    --dry-run=client -o yaml | kubectl apply -f -

                    kubectl rollout status deployment/exam-app
                    kubectl get pods
                    kubectl get svc exam-service
                '
                '''
            }
        }
    }
}
