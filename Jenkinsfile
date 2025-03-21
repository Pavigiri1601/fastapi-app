pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-fastapi-app"
        CONTAINER_NAME = "fastapi-container"
        AWS_EC2_IP = "YOUR_EC2_PUBLIC_IP"
        DOCKER_HUB_USER = "YOUR_DOCKERHUB_USERNAME"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/YOUR_USERNAME/fastapi-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker tag $IMAGE_NAME $DOCKER_HUB_USER/$IMAGE_NAME:latest'
                    sh 'docker push $DOCKER_HUB_USER/$IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@$AWS_EC2_IP << EOF
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true
                    docker pull $DOCKER_HUB_USER/$IMAGE_NAME:latest
                    docker run -d -p 8000:8000 --name $CONTAINER_NAME $DOCKER_HUB_USER/$IMAGE_NAME:latest
                    EOF
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}

