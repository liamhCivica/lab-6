pipeline {
    agent any
    stages {
        stage('Clean up') {
            steps {
                sh '''
                docker stop app-db || true && docker rm app-db || true
                docker stop app || true && docker rm app || true
                docker stop nginx || true && docker rm nginx || true
                docker network rm new-network
                docker volume rm mysql
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
                sh "docker network create new-network"
                sh "docker volume create mysql"
                sh "docker run -d -p 3306:3306 --network new-network --name mysql -v mysql:/var/lib/mysql app-db app-db"
                sh "docker run -d -p 5000:5000 --network new-network --name mysql app"
                sh "docker run -d -p 80:80 --network new-network --name nginx --mount type=bind,source="$(pwd)"/nginx,target=/etc/nginx nginx"
            }
        }
        stage('Push') {
            steps {
                sh "docker tag app-db liamhillsaffron/app-db"
                sh "docker push liamhillsaffron/app-db"
                sh "docker tag app liamhillsaffron/app"
                sh "docker push liamhillsaffron/app"
            }
        }
    }
}
