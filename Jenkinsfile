pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Harish2B/flask-docker-k8s.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t flask-docker-k8s:latest .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // replace with your DockerHub username
                    withDockerRegistry([ credentialsId: 'harishbalaji2404@outlook.com', url: '' ]) {
                        sh 'docker tag flask-docker-k8s:latest <your-dockerhub-username>/flask-docker-k8s:latest'
                        sh 'docker push <your-dockerhub-username>/flask-docker-k8s:latest'
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f k8s-deployment.yaml'
                }
            }
        }
    }
}
