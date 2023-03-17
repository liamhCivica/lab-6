pipeline {
    agent any
    stages {
        stage('Clean up') {
            steps {
                sh '''
                docker stop app-db || true && docker rm app-db || true
                docker stop app || true && docker rm app || true
                docker stop nginx || true && docker rm nginx || true
                '''
            }
        }
        stage('Build') {
            steps {
                sh "docker build -t app-db ./db"
                sh "docker build -t app ./flask-app"
            }
        }
        stage('Deploy') {
            steps {
                sh "docker run -d -p 3306:3306 --name mysql app-db"
                sh "docker run -d -p 5000:5000 --name mysql app"
                sh "docker run -d -p 80:80 --name mysql app"
            }
        }
    }
}
