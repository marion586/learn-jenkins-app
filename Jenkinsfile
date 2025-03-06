pipeline {
    agent any
    // This is a comment
   /*  this is a
    block 
    comment
    */
    environment {
        NETLIFY_SITE_ID = "b9d5e0fe-be53-4b2b-b016-be858baea315"
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
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
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML playwright local Report', reportTitles: '', useWrapperFileDirectly: true])
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
                    echo 'Small change'
                    npm install netlify-cli 
                    node_modules/.bin/netlify --version
                    echo "Delpoing to staging. Site Id: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build
                '''
            }
          
        }
       stage('Deploy prod') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo 'Small change'
                    npm install netlify-cli 
                    node_modules/.bin/netlify --version
                    echo "Delpoing t production. Site Id: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build  --prod
                '''
            }
          
        } 
        stage('Prod E2E'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            // the reson is we need t it only here 
             environment {
                CI_ENVIRONMENT_URL = "https://majestic-sorbet-2c883e.netlify.app"
            }
            steps {
        
                sh ''' 
                    npx playwright test --reporter=html
                '''
                
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML playwright e2e Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }
    }

    
}