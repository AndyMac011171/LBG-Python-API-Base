pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.50 << EOF
                docker network create jenk-network || echo "Network Already Exists"
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                docker stop nginx || echo "nginx Not Running"
                docker rm nginx || echo "nginx Not Running"
                '''
           }
        }
        stage('Build') {
            steps {
                sh '''
                docker build -t andymac011171/python-api -t andymac011171/python-api:v${BUILD_NUMBER} .                  
                docker build -t docker.io/andymac011171/nginx-jenk:latest -t docker.io/andymac011171/nginx-jenk:v${BUILD_NUMBER} ./nginx
                '''
           }
        }
        stage('Push') {
            steps {
                sh '''
                docker push andymac011171/python-api
                docker push andymac011171/python-api:v${BUILD_NUMBER}
                docker push docker.io/andymac011171/nginx-jenk:latest
                docker push docker.io/andymac011171/nginx-jenk:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.50 << EOF
                docker run -d --name flask-app --network jenk-network docker.io/andymac011171/flask-jenk
                docker run -d -p 80:80 --name nginx --network jenk-network docker.io/andymac011171/nginx-jenk:latest
                '''
            }
        }
        stage('Cleanup') {
            steps {
                sh '''
                docker system prune -f 
                '''
           }
        }
    }
}