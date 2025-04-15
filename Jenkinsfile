pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials-id' // Jenkins credentials ID for Docker Hub
        DOCKER_IMAGE = 'karthikeyank2004/myapp'
        IMAGE_TAG = 'latest'
        CONTAINER_NAME = 'earthly-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Karthikeyan-Karunakaran/earthly-cicd.git', branch: 'main'
            }
        }

        stage('Build Docker Image with Earthly') {
            steps {
                sh 'earthly --ci --load +docker'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${env.DOCKER_HUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag $DOCKER_IMAGE:$IMAGE_TAG $DOCKER_IMAGE:$IMAGE_TAG
                        docker push $DOCKER_IMAGE:$IMAGE_TAG
                    """
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh """
                    docker pull $DOCKER_IMAGE:$IMAGE_TAG
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true
                    docker run -d --name $CONTAINER_NAME -p 3000:3000 $DOCKER_IMAGE:$IMAGE_TAG
                """
            }
        }
    }

    post {
        cleanup {
            sh 'docker system prune -f'
        }
    }
}
