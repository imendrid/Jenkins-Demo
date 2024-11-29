pipeline {
    agent any

    environment {
        registry = "imendrid/demo-jenkins"
        registryCredential = 'dockerhub_id'
        dockerImage = ''
        GIT_CREDENTIALS = 'jenkins access token'  // Name of your credentials
    }

    triggers {
        pollSCM('*/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: "${GIT_CREDENTIALS}", url: 'https://github.com/imendrid/Jenkins-Demo.git'
            }
        }

        stage('Build App') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Build image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Deploy Docker container') {
            steps {
                sh "docker run --name demo-jenkins -d -p 2222:2222 $registry:$BUILD_NUMBER"
            }
        }

        stage('Deploy K8S') {
            steps {
                sh "kubectl apply -f k8s.yml"
            }
        }

        stage('Verify deployment') {
            steps {
                sh "kubectl get pods"
                sh "kubectl get svc"
            }
        }

        stage('Clean up') {
            steps {
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
    }

    post {
        success {
            echo 'Pipeline execution successful!'
        }
        failure {
            echo 'Pipeline execution failed.'
        }
    }
}
