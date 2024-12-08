node {
def mvnHome = tool 'maven-3.5.2'
def dockerImage
def dockerImageTag = "devopsexample:${env.BUILD_NUMBER}"
stage('Clone Repo') {
git branch: 'main', url: 'https://github.com/imendrid/Jenkins-Demo.git'

}
stage('Build Project') {
sh "mvn clean package"
}
stage('Initialize Docker'){
def dockerHome = tool 'MyDocker'
env.PATH = "${dockerHome}/bin:${env.PATH}"
}
stage('Build Docker Image') {
sh "docker build -t ${dockerImageTag} ."
}
stage('Deploy Docker Image'){
echo "Docker Image Tag Name: ${dockerImageTag}"
sh "docker run --name ${CONTAINER_NAME} -d -p 2222:2222 ${dockerImageTag}"

}
}
