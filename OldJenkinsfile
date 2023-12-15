pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') 
                    {
                        sh '''
                            ssh -i ~/.ssh/id_rsa jenkins@10.154.0.50 << EOF

                            echo "executing main branch"
                            docker network create project-network || echo "Network Already Exists"

                            docker stop flask-app || echo "flask-app not running"
                            docker rm flask-app || echo "flask-app not running"
                            docker stop nginx || echo "nginx Not Running"
                            docker rm nginx || echo "nginx Not Running"

                            docker rmi andymac011171/nginx-jenk || echo "Image does not exist"
                            docker rmi andymac011171/python-api || echo "Image does not exist"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                            ssh -i ~/.ssh/id_rsa jenkins@10.154.0.57 << EOF
                           
                            echo "executing dev branch"
                            docker network create project-network || echo "Network Already Exists"

                            docker stop flask-app || echo "flask-app not running"
                            docker rm flask-app || echo "flask-app not running"
                            docker stop nginx || echo "nginx Not Running"
                            docker rm nginx || echo "nginx Not Running"

                            docker rmi andymac011171/nginx-jenk || echo "Image does not exist"
                            docker rmi andymac011171/python-api || echo "Image does not exist"
                        '''
                    } else {
                        sh '''
                            echo "Unrecognised branch"
                        '''
                    }
		        }
           }
        }
        stage('Build') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        echo "Build not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t andymac011171/python-api -t andymac011171/python-api:v${BUILD_NUMBER} .                  
                        docker build -t docker.io/andymac011171/nginx-jenk:latest -t docker.io/andymac011171/nginx-jenk:v${BUILD_NUMBER} ./nginx
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
		            }

                }
        }
        stage('Push') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        echo "Build not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker push andymac011171/python-api
                        docker push andymac011171/python-api:v${BUILD_NUMBER}
                        docker push docker.io/andymac011171/nginx-jenk:latest
                        docker push docker.io/andymac011171/nginx-jenk:v${BUILD_NUMBER}
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
		        }
                
           }
        }
        stage('Deploy') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') 
                    {
                        sh '''
                            ssh -i ~/.ssh/id_rsa jenkins@10.154.0.50 << EOF
                            docker run -d --name flask-app --network project-network docker.io/andymac011171/python-api
                            docker run -d -p 80:80 --name nginx --network project-network docker.io/andymac011171/nginx-jenk:latest
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                            ssh -i ~/.ssh/id_rsa jenkins@10.154.0.57 << EOF
                            docker run -d --name flask-app --network project-network docker.io/andymac011171/python-api
                            docker run -d -p 80:80 --name nginx --network project-network docker.io/andymac011171/nginx-jenk:latest
                        '''
                    } else {
                        sh '''
                            echo "Unrecognised branch"
                        '''
                    }
		        }
              
            }
                
        }
        stage('Cleanup') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/dev') {
                      sh '''
                      docker rmi andymac011171/python-api:v${BUILD_NUMBER}
                      docker rmi andymac011171/nginx-jenk:v${BUILD_NUMBER}
                      '''  
                    }
                }
                
                sh '''
                docker system prune -f 
                '''
            }
        }
    }
}