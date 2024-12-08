pipeline {
    agent any

    environment {
        registry = "imendrid/demo-jenkins"
        registryCredential = 'dockerhub_id'
        dockerImage = ''
        GIT_CREDENTIALS = 'jenkins access token'
    }

    triggers {
        pollSCM('*/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: GIT_CREDENTIALS, url: 'https://github.com/imendrid/Jenkins-Demo.git'
            }
        }

        stage('Build App') {
            steps {
                script {
                    docker.image('maven:3.8.7').inside {
                        sh 'mvn clean package'
                    }
                }
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
                sh """
                docker stop demo-jenkins || true
                docker rm demo-jenkins || true
                docker run --name demo-jenkins -d -p 2222:2222 $registry:$BUILD_NUMBER
                """
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
                sh """
                docker stop demo-jenkins || true
                docker rm demo-jenkins || true
                docker rmi $registry:$BUILD_NUMBER || true
                """
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
