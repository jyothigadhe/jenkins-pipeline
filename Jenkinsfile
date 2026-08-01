 pipeline {

    agent any

    environment {
        DOCKER_IMAGE = "gadhe/myapp:latest"
        DOCKER_CREDENTIALS = "dockerhub-creds"
        CONTAINER_NAME = "mycontainer"
        PORT = "8081"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com//jyothigadhe/jenkins-pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build --no-cache -t $DOCKER_IMAGE .
                '''
            }
        }

        stage('Login and Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDENTIALS}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker push $DOCKER_IMAGE

                    docker logout
                    '''
                }
            }
        }

        stage('Clean Old Container') {
            steps {
                sh '''
                echo "Removing old container..."

                docker stop $CONTAINER_NAME 2>/dev/null || true

                docker rm $CONTAINER_NAME 2>/dev/null || true

                echo "Old container removed"
                '''
            }
        }

        stage('Deploy New Container') {
            steps {
                sh '''
                echo "Starting new container..."

                docker run -d \
                --name $CONTAINER_NAME \
                -p $PORT:80 \
                $DOCKER_IMAGE

                echo "Deployment completed"

                docker ps
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline Successful"
        }

        failure {
            echo "Pipeline Failed"
        }
    }
}
