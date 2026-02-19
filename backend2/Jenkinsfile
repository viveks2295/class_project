pipeline {
    agent any

    stages {

        stage('Build Docker Image') {
            steps {
                dir('backend2') {
                    sh 'docker build -t flaskapp .'
                }
            }
        }

        stage('Stop Old Container') {
            steps {
                sh 'docker stop flaskapp || true'
                sh 'docker rm flaskapp || true'
            }
        }

        stage('Run New Container') {
            steps {
                sh 'docker run -d -p 80:5000 --name flaskapp flaskapp'
            }
        }
    }
}
