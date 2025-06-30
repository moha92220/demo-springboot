pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'jkeke33'
        DOCKERHUB_REPO = 'demo-app'
    }

    stages {
        stage('Build') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh './mvnw test'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKERHUB_USER/$DOCKERHUB_REPO:latest .'
            }
        }

        stage('Push DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-password', variable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u $DOCKERHUB_USER --password-stdin
                        docker push $DOCKERHUB_USER/$DOCKERHUB_REPO:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker rm -f demo-running || true'
                sh 'docker run -d -p 8080:8080 --name demo-running $DOCKERHUB_USER/$DOCKERHUB_REPO:latest'
            }
        }
    }
}
