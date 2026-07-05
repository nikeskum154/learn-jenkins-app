pipeline {
    agent any
    environment {
        NETLIFY_SITE_ID = '4ca87c6a-1bec-4053-9773-2c4df801510b'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node'
                    reuseNode true
                }
            }
            steps {
                echo "Build Stage"
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
        
        stage(Tests) {
            parallel {
                stage('Test') {
                    agent {
                        docker {
                            image 'node'
                            reuseNode true
                        }
                    }
                    steps {
                        echo "Test Stage"
                        sh '''
                            # test -f build/index.html
                            npm test
                        '''    
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }

                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        echo "E2E Test Stage"
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            npx playwright test --reporter=html
                        '''    
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local E2E', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }    
        }
        stage('Deploy Staging') {
            agent {
                docker {
                    image 'node'
                    reuseNode true
                }
            }
            steps {
                echo "Deploy Stage"
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: ${NETLIFY_SITE_ID}"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build
                '''    
            }    
        }
        stage('Deploy Prod') {
            agent {
                docker {
                    image 'node'
                    reuseNode true
                }
            }
            steps {
                echo "Deploy Stage"
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: ${NETLIFY_SITE_ID}"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''    
            }    
        }
        stage('Prod E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }      
            }
            environment {
                    CI_ENVIRONMENT_URL = "https://jocular-caramel-84488e.netlify.app/"
            }
            steps {
                echo "Prod E2E Test Stage Post Deployment"
                sh '''
                    npx playwright test --reporter=html
                '''    
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Prod E2E', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }        
        
    }
}
