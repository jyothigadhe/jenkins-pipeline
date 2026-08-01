pipeline {
    agent any

    stages {

        stage('Build Image') {
            steps {
                sh 'docker build -t gadhe/myapp:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d --name myapp -p 80:80 gadhe/myapp:latest'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push gadhe/myapp:latest
                    '''
                }
            }
        }
    }
}
