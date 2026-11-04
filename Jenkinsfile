pipeline {

    agent any

    environment {
        DOCKER_IMAGE = "tanya5/tanya:1.0"
        CONTAINER_NAME = "tanya-container"
    }

    stages {

        stage('Pull Code from GitHub') {
            steps {
                checkout scm
                echo 'Code pulled successfully'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy Container Locally') {
            steps {
                sh '''
                    docker rm -f $CONTAINER_NAME || true
                    docker run -d --name $CONTAINER_NAME -p 8080:80 $DOCKER_IMAGE
                '''
            }
        }
    }
}
