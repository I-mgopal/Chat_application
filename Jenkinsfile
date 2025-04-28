pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'gopal89/chat_app'
        CONTAINER_NAME = 'chatbot_container'
        HOST_PORT = '5000'
        CONTAINER_PORT = '80'
    }
    stages {
        stage('Pull from GitHub') {
            steps {
                // Replace with your repo URL if different
                git 'https://github.com/I-mgopal/Chat_application.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_IMAGE%:${BUILD_NUMBER} ."
            }
        }
        stage('Run Docker Container') {
            steps {
                bat """
                docker stop %CONTAINER_NAME% || exit 0
                docker rm %CONTAINER_NAME% || exit 0
                docker run -d --name %CONTAINER_NAME% -p %HOST_PORT%:%CONTAINER_PORT% %DOCKER_IMAGE%:${BUILD_NUMBER}
                """
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat """
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    docker tag %DOCKER_IMAGE%:${BUILD_NUMBER} %DOCKER_IMAGE%:latest
                    docker push %DOCKER_IMAGE%:${BUILD_NUMBER}
                    docker push %DOCKER_IMAGE%:latest
                    """
                }
            }
        }
    }
}
