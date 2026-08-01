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
                url: ' https://github.com/jyothigadhe/jenkins-pipeline.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE .
                '''
            }
        }

        stage('Docker Login & Push') {
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

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop $CONTAINER_NAME || true

                docker rm $CONTAINER_NAME || true

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
            echo "Deployment successful"
        }

        failure {
            echo "Pipeline failed"
        }
    }
}
