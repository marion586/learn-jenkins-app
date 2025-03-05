pipeline {
    agent any
    // This is a comment
   /*  this is a
    block 
    comment
    */
    stages {
        /*stage('Build') {
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
                    
                '''
            }
          
        } */
        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "Test stage"
                sh '''
                    test -f build/index.html
                    npm test
                '''
                
            }
        }

        stage('E2E'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.50.1-noble'
                    reuseNode true
                }
            }
            steps {
        
                sh '''
                    npm install -g serve
                    serve -s build
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