Flask App CI/CD with Jenkins, Docker, and AWS EC2

This project demonstrates a CI/CD pipeline using Jenkins, Docker, and AWS EC2.
The pipeline automatically builds a Docker image for a Flask application, pushes it to Docker Hub, and deploys it on an EC2 instance.

🚀 Workflow Overview

Developer pushes code to GitHub

Jenkins Pipeline triggers:

Clone the repository

Build Docker image

Push Docker image to Docker Hub

Deploy a container on AWS EC2

Flask app runs on EC2 at port 5000

🛠️ Prerequisites

AWS EC2 instance (Ubuntu recommended)

Docker & Docker Compose installed

Jenkins is installed and running

GitHub repository with your Flask app & Dockerfile

Docker Hub account

📂 Project Structure
jenkins-docker-aws-pipeline/
│-- app.py              # Flask app
│-- requirements.txt    # Python dependencies
│-- Dockerfile          # Docker build instructions
│-- Jenkinsfile         # Jenkins pipeline configuration
│-- README.md           # Documentation

📦 Docker Setup
1. Build Docker Image
docker build -t flask-app:1.0.

2. Run Container
docker run -d -p 5000:5000 --name flask-app-test flask-app:1.0

3. Check Logs
docker logs flask-app-test

4. Stop & Remove Container
docker stop flask-app-test
docker rm flask-app-test

🔑 Docker Hub Login

Login to Docker Hub from EC2:

docker login -u <your-dockerhub-username>


Verify credentials are stored:

cat ~/.docker/config.json

⚙️ Jenkins Pipeline Setup
1. Add Credentials in Jenkins

GitHub PAT → github-pat

Docker Hub Password/Token → docker-hub-pass

2. Configure Jenkinsfile
pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'your-dockerhub-username'
        DOCKER_IMAGE = 'your-dockerhub-username/flask-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git(
                    url: 'https://github.com/Abhi-mishra998/jenkins-docker-aws-pipeline.git',
                    credentialsId: 'github-pat'
                )
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE:$BUILD_NUMBER ."
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

🌐 Access the App

Once deployed, visit:

http://<EC2-PUBLIC-IP>:5000

📜 Useful Commands
List Docker Images
docker images

List Running Containers
docker ps

Remove Unused Images
docker image prune -a

Check EC2 Public IP
curl ifconfig.me

✅ CI/CD Flow Recap

Code pushed → GitHub

Jenkins pulls code → Builds Docker image

Image pushed → Docker Hub

EC2 pulls the latest image → Runs the container

App live at http://<EC2-IP>:5000

🔒 Security Note:
Always use Personal Access Tokens (PATs) or Secrets in Jenkins instead of plain-text passwords.
