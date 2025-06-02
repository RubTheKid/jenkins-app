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
                    echo "Building the project"
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
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }

            stage('Deploy staging') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    echo "Deploying to Staging with site ID: $NETLIFY_SITE_ID"

                    npx netlify status
                    npx netlify deploy --dir=build
                '''
            }   
        }

        stage('Deploy production') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    echo "Authenticating with Netlify using the API token"
                    export NETLIFY_AUTH_TOKEN=$NETFLIFY_AUTH_TOKEN

                    npx netlify status


                    echo "Deploying to Netlify with site ID: $NETLIFY_SITE_ID"
                    npx netlify deploy --dir=build --prod
                '''
            }   
        }


        stage ('Prod E2E'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
                environment {
                    CI_ENVIRONMENT_URL = 'https://startling-kitsune-506188.netlify.app'
                }
                steps {
                    sh '''
                        echo "Running E2E tests against the production site"
                        npx playwright test --reporter=html,junit
                    '''
                }

                    post {
                    always {
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright E2E Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                
            }
        }
    }
}

//script for habilitating the Playwright plugin in Jenkins 
// Try to run this script in Jenkins Dashboard > Manage Jenkins > section “Tools and actions” > Script Console:

// System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "sandbox allow-scripts;")
