pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'your-dockerhub-username'
        DOCKER_IMAGE = 'your-dockerhub-username/flask-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/your-username/your-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-pass', variable: 'DOCKER_HUB_PASS')]) {
                    sh """
                    echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin
                    docker push $DOCKER_IMAGE:$BUILD_NUMBER
                    """
                }
            }
        }

        stage('Deploy on EC2') {
            steps {
                sh """
                docker stop flask-app || true
                docker rm flask-app || true
                docker run -d --name flask-app -p 5000:5000 $DOCKER_IMAGE:$BUILD_NUMBER
                """
            }
        }
    }
}
