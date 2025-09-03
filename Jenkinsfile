pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "harish2b/flask-docker-k8s"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Harish2B/flask-docker-k8s.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('sonarcube') {
                        withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_AUTH_TOKEN')]) {
                            sh """
                                ${scannerHome}/bin/sonar-scanner \
                                  -Dsonar.projectKey=sonarqube-check \
                                  -Dsonar.projectName='sonarqube check' \
                                  -Dsonar.sources=. \
                                  -Dsonar.login=$SONAR_AUTH_TOKEN
                            """
                        }
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry([ credentialsId: 'dockerhub-creds', url: '' ]) {
                        sh "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl get pods'
                }
            }
        }

        stage('Access Service') {
            steps {
                script {
                    // Get minikube IP and NodePort for direct URL
                    def minikubeIp = sh(script: "minikube ip", returnStdout: true).trim()
                    def nodePort = sh(script: "kubectl get svc flask-service -o=jsonpath='{.spec.ports[0].nodePort}'", returnStdout: true).trim()
                    echo "✅ Your app is running at: http://${minikubeIp}:${nodePort}"
                }
            }
        }
    }
}

