pipeline {
    agent any

    environment {
        IMAGE_NAME = 'gopal89/chat_app'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Pull from GitHub') {
            steps {
                git url: 'https://github.com/I-mgopal/Chat_application.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
                bat "docker tag %IMAGE_NAME%:%IMAGE_TAG% %IMAGE_NAME%:latest"
            }
        }

        stage('Run Docker Container') {
            steps {
                bat '''
                docker stop chatbot_container || exit 0
                docker rm chatbot_container || exit 0
                docker run -d --name chatbot_container -p 5000:5000 %IMAGE_NAME%:%IMAGE_TAG%
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                    docker push %IMAGE_NAME%:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            bat 'docker logout'
            cleanWs()
        }
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
    }
}
