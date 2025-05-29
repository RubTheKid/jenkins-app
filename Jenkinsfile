pipeline {
    agent any

    stages {
        stage('Build') {
              agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }   
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Checking if index.html exists"
                    test -f build/index.html
                    npm test
                '''
            }
        }

          stage('E2E Test') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.52.0-noble'
                    reuseNode true
                }
            }
                steps {
                    sh '''
                        npm install serve
                        npx serve -s build &
                        sleep 10
                        npx playwright test
                    '''
                }

        }
    }

    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
