pipeline {
    agent any

    stages {
        stage('withoutDocker') {
            steps {
                sh '''
                 echo "Without Docker"
                 ls -la 
                 touch container-no.txt
                '''
            }
        }
        stage('withDocker') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                echo "With docker"
                ls -la 
                touch container-yes.txt
                '''
            }
        }
    }
}
