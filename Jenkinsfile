pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "Pavi1601/fastapi-app:latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'github-ssh-key', url: 'git@github.com:Pavigiri1601/fastapi-app.git', branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }
    }
}
