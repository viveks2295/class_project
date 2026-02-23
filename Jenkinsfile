pipeline {
    agent any

    environment {
        IMAGE_NAME = "singhjii/flask-ci-app"   
        IMAGE_TAG = "latest"
        CONTAINER_NAME = "flaskapp"
        DOCKER_CREDS = "dockerhub-creds"     
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/viveks2295/class_project.git'
            }
        }

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
                    credentialsId: "$DOCKER_CREDS",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true
                '''
            }
        }

        stage('Run New Container') {
            steps {
                sh '''
                docker run -d -p 80:5000 --name $CONTAINER_NAME $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Cleanup Docker Images') {
            steps {
                sh 'docker image prune -f'
            }
        }
    }

    post {
        success {
            echo " Deployment Successful"
        }
        failure {
            echo " Deployment Failed"
        }
    }
}
