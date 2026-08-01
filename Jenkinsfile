pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                echo 'building'
            }
        }
        stage('test') {
            steps {
                echo 'test'
            }
        }
        stage('docker build') {
            steps {
                sh 'docker build -t myapp:latest'
            }
        }
        stage('deploy') {
            steps {
                echo 'diploy'
            }
        }
    }
}
