pipeline {
    agent any

    environment {
        registry = "wahidh007/demo-jenkins"
        registryCredential = 'docker-hub-credentials'
        CONTAINER_NAME = 'demo-jenkins'
    }

    triggers {
        pollSCM('*/5 * * * *') // ou utilisez un webhook GitHub pour un déclenchement immédiat
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/wahid007/Jenkins-Demo.git'
            }
        }

        stage('Validate Docker Installation') {
            steps {
                sh 'docker --version'
                echo 'Docker installation validated successfully.'
            }
        }

        stage('Build App') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    def existingContainer = sh(script: "docker ps -a -q -f name=${CONTAINER_NAME}", returnStdout: true).trim()
                    
                    if (existingContainer) {
                        echo "Stopping and removing existing container: ${existingContainer}"
                        sh "docker rm -f ${existingContainer}"
                    }

                    echo "Deploying new container..."
                    sh "docker run --name ${CONTAINER_NAME} -d -p 2222:2222 ${registry}:${BUILD_NUMBER}"

                    slackSend color: "good", message: "${registry}:${BUILD_NUMBER} - Image successfully created and deployed! :man_dancing:"
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline execution successful!'
            slackSend color: "good", message: "Pipeline execution successful! :man_dancing:"
        }
        failure {
            echo 'Pipeline execution failed.'
            slackSend color: "danger", message: "Pipeline execution failed! :ghost:"
        }
    }
}
