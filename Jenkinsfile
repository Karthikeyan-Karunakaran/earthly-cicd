pipeline {
    agent any
    environment {
        IMAGE_NAME = "myrepo/node-earthly-app:latest"
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Karthikeyan-Karunakaran/earthly-cicd.git'
            }
        }
        stage('Build with Earthly') {
            steps {
                sh 'earthly +build'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'earthly +docker'
            }
        }
        stage('Push to Docker Registry') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub', url: 'https://index.docker.io/v1/']) {
                    sh "docker tag ${IMAGE_NAME} mydockerhub/node-earthly-app:latest"
                    sh "docker push mydockerhub/node-earthly-app:latest"
                }
            }
        }
        stage('Deploy') {
            steps {
                sh "docker run -d -p 3000:3000 mydockerhub/node-earthly-app:latest"
            }
        }
    }
}

