pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        KUBECONFIG = '/path/to/kubeconfig' // Path to Kubernetes config file
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build and Push Docker Images') {
            parallel {
                stage('Build Frontend Image') {
                    steps {
                        script {
                            sh """
                            echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                            docker build -t ramvn/frontend-service:latest ./frontend
                            docker push ramvn/frontend-service:latest
                            """
                        }
                    }
                }
                stage('Build Backend Image') {
                    steps {
                        script {
                            sh """
                            echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                            docker build -t ramvn/backend-service:latest ./backend
                            docker push ramvn/backend-service:latest
                            """
                        }
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s/frontend-deployment.yaml
                kubectl apply -f k8s/backend-deployment.yaml
                kubectl apply -f k8s/ingress.yaml
                '''
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished!'
        }
    }
}
