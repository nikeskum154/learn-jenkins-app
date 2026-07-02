pipeline {
    agent any

    stages {
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
        stage('Test') {
            agent{
                agent any
                reuseNode true
            }
            steps {
                sh '''
                    test build/index.html
                    npm test
                '''    
            }

        }
    }
}
