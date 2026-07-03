pipeline {
    agent any

    stages {
        /*
        stage('Build') {
            agent {
                docker {
                    image 'node'
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
        */
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
                        echo "E2E Stage"
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            npx playwright test --reporter=html
                        '''    
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }    
        }        
        
    }
}
