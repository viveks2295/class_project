pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "singhjii/flask-ci-app"
        DOCKER_TAG = "latest"
        EC2_IP = "YOUR_EC2_PUBLIC_IP"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/YOUR_USERNAME/YOUR_REPO.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-creds', url: '']) {
                    sh 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-server-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ec2-user@$EC2_IP '
                        docker pull $DOCKER_IMAGE:$DOCKER_TAG &&
                        docker stop flask-app || true &&
                        docker rm flask-app || true &&
                        docker run -d -p 80:5000 --name flask-app $DOCKER_IMAGE:$DOCKER_TAG
                    '
                    """
                }
            }
        }
    }
}
