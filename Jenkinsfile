pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub-credentials-id' // Jenkins credentials ID for Docker Hub
        DOCKER_IMAGE = 'karthikeyank2004/myapp'
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
                script {
                    // Get the commit count to use in version tagging
                    def commitCount = sh(script: "git rev-list --count HEAD", returnStdout: true).trim()
                    def versionTag = "v1.0.${commitCount}"
                    env.VERSION_TAG = versionTag
                    sh "earthly --ci --output +docker --VERSION ${env.VERSION_TAG}"
                }
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${env.DOCKER_HUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag $DOCKER_IMAGE:$VERSION_TAG $DOCKER_IMAGE:$VERSION_TAG
                        docker push $DOCKER_IMAGE:$VERSION_TAG
                    """
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh """
                    docker pull $DOCKER_IMAGE:$VERSION_TAG
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true
                    docker run -d --name $CONTAINER_NAME -p 3000:3000 $DOCKER_IMAGE:$VERSION_TAG
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
