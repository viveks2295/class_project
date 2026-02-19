pipeline {
    agent any

    environment {
        IMAGE_NAME = "viveks2295/flask-ci-app"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                dir('backend2') {
                    sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop flaskapp || true
                docker rm flaskapp || true
                docker pull $IMAGE_NAME:$IMAGE_TAG
                docker run -d -p 80:5000 --name flaskapp $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
