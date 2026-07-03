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
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.61.0-noble'
                    reuseNode true
                }
            }
            steps {
                echo "E2E Stage"
                sh '''
                    npm -g install serve
                    node_modules/.bin/serve -s build
                    npx playwright test
                '''    
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
