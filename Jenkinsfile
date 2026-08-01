pipeline {

    agent any

    environment {
        DOCKER_IMAGE = "gadhe/myapp:latest"
        DOCKER_CREDENTIALS = "dockerhub-creds"
        CONTAINER_NAME = "mycontainer"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/jyothigadhe/jenkins-pipeline.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE .
                '''
            }
        }

        stage('Docker Login and Push') {
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

        stage('Remove Old Container') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME 2>/dev/null || true
                '''
            }
        }

        stage('Deploy New Container') {
            steps {
                sh '''
                docker run -d \
                --name $CONTAINER_NAME \
                -p 8080:80 \
                $DOCKER_IMAGE
                '''
            }
        }
    }

    post {
        success {
            echo "Build and Deployment Successful"
        }

        failure {
            echo "Pipeline Failed"
        }
    }
}
