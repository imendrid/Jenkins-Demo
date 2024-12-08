node {
    def mvnHome = tool 'maven-3.5.2'
    def dockerImageTag = "devopsexample:${env.BUILD_NUMBER}" // Ensure BUILD_NUMBER is provided
    stage('Clone Repo') {
        checkout([$class: 'GitSCM',
                  branches: [[name: '*/main']],
                  userRemoteConfigs: [[url: 'https://github.com/wahid007/Jenkins-Demo.git']]
        ])
    }
    stage('Build Project') {
        sh "${mvnHome}/bin/mvn clean package" // Use mvnHome explicitly
    }
    stage('Initialize Docker') {
        def dockerHome = tool 'MyDocker'
        env.PATH = "${dockerHome}/bin:${env.PATH}"
    }
    stage('Build Docker Image') {
        sh "docker build -t ${dockerImageTag} ." // Corrected quoting
    }
    stage('Deploy Docker Image') {
        echo "Docker Image Tag Name: ${dockerImageTag}"
        sh "docker run --name devopsexample -d -p 3333:2222 ${dockerImageTag}"

    }
}
