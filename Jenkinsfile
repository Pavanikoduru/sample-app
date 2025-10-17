pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "pavanikoduru22/sample-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url:'https://github.com/Pavanikoduru/sample-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker stop sample-app || true
                    docker rm sample-app || true
                    docker pull $DOCKER_IMAGE:latest
                    docker run -d --name sample-app -p 3000:3000 $DOCKER_IMAGE:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Dockerized CI/CD Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
