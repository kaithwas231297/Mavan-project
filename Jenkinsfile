pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'atul2907'
        IMAGE_NAME = 'atul2907/myapp'
    }

    tools {
        maven 'Maven'
    }

    stages {

        stage('Pull from GitHub') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/kaithwas231297/Mavan-project.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Run Docker Container') {
            steps {
                sh "docker stop myapp-container || true"
                sh "docker rm myapp-container || true"
                sh "docker run -d --name myapp-container -p 8090:8080 ${IMAGE_NAME}:latest"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}:latest"
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed! Image pushed to atul2907/myapp'
        }
        failure {
            echo 'Pipeline failed — check logs above.'
        }
    }
}
