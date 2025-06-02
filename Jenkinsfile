pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '74cd9dd7-2341-40a3-8326-6cfa38933759'
        NETFLIFY_AUTH_TOKEN = credentials('netflify-token')
    }

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

        stage('Run Tests') {
            parallel {
                stage('Unit Tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            echo "Running unit tests"
                            #test -f build/index.html
                            npm test
                        '''
                    }
                     post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }
                stage('E2E Tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm install serve
                            npx serve -s build &
                            sleep 10
                            npx playwright test --reporter=html,junit
                        '''
                    }

                     post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }

            stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    npx netlify --version
                    echo "Deploying to Netlify with site ID: $NETLIFY_SITE_ID"
                    npx netlify status
                '''
            }   
        }
    }
}

//script for habilitating the Playwright plugin in Jenkins 
// Try to run this script in Jenkins Dashboard > Manage Jenkins > section “Tools and actions” > Script Console:

// System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "sandbox allow-scripts;")
