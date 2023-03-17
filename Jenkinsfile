pipeline {
    agent any
    
    environment {
      MYSQLPASSWORD = credentials('mysqlpassword')
    }
    
    stages {
        stage('Clean up') {
            steps {
                sh '''
                docker stop mysql || true && docker rm mysql || true
                docker stop flask-app || true && docker rm flask-app || true
                docker stop nginx || true && docker rm nginx || true
                docker network rm new-network || true
                docker volume rm mysql || true
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
                sh "docker run -d -p 3306:3306 --network new-network -e MYSQL_ROOT_PASSWORD=${MYSQLPASSWORD} --name mysql -v mysql:/var/lib/mysql app-db"
                sh "docker run -d -p 5000:5000 --network new-network -e MYSQL_ROOT_PASSWORD=${MYSQLPASSWORD} --name flask-app -e  app"
                sh "docker run -d -p 80:80 --network new-network --name nginx --mount type=bind,source=\"\$(pwd)\"/nginx,target=/etc/nginx nginx"
            }
        }
        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh "docker login -u $USERNAME -p $PASSWORD"
                }
                sh "docker tag app-db liamhillsaffron/app-db"
                sh "docker push liamhillsaffron/app-db"
                sh "docker tag app liamhillsaffron/app"
                sh "docker push liamhillsaffron/app"
            }
        }
    }
}
