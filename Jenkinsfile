pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id') // Add Docker Hub credentials in Jenkins
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
                            docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
                                def frontendImage = docker.build("frontend-service:${env.BUILD_NUMBER}", "./frontend")
                                frontendImage.push('latest')
                            }
                        }
                    }
                }
                stage('Build Backend Image') {
                    steps {
                        script {
                            docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
                                def backendImage = docker.build("backend-service:${env.BUILD_NUMBER}", "./backend")
                                backendImage.push('latest')
                            }
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
