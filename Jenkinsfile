pipeline {
    agent any
    // This is a comment
   /*  this is a
    block 
    comment
    */
    environment {
        NETLIFY_SITE_ID = "c34d7660-9d57-4f13-8ba4-10109a276104"
    }
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
          
        }*/ 
        stage('Run Tests') {
            parallel {
            stage('Unit Test'){
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
            post {
                always {
                    junit 'jest-results/junit.xml'
                }
            }
        }

            stage('E2E'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
        
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10  
                    npx playwright test --reporter=html
                '''
                
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
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
                    npm install netlify-cli 
                    node_modules/.bin/netlify --version
                    echo "Delpoing t production. Site Id: $NETLIFY_SITE_ID"
                '''
            }
          
        } 
        
    }

    
}