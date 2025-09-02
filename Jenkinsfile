pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'Abhi-mishra998'  // your Docker Hub username
        DOCKER_IMAGE = 'Abhi-mishra998/jenkins-docker-aws-pipeline'  // docker image name
    }

    stages {
        stage('Clone Repo') {
            steps {
                // Use your GitHub repo URL
                git url: 'https://github.com/Abhi-mishra998/jenkins-docker-aws-pipeline.git', branch: 'main',
                    credentialsId: 'github-credentials-id' // add your Jenkins GitHub credentials ID
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
