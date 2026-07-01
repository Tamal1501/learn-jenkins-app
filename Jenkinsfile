pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '81dfefce-3ae9-4344-94ad-b9659299879d'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            environment {
                NPM_CONFIG_REGISTRY = credentials('npm-registry')
            }
            steps {
                sh '''
                    echo "Building the project..."
                    ls -la
                    node --version
                    npm --version
                    rm -rf node_modules
                    npm cache clean --force
                    npm ci --cache .npm --prefer-offline=false
                    npm run build
                    ls -la
                '''
            }
        }

        stage ('Tests') {
            parallel {
                stage ('Unit Tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    environment {
                        NPM_CONFIG_REGISTRY = credentials('npm-registry')
                    }
                    steps {
                        sh '''
                            test -f build/index.html
                            npm test
                        '''
                    }
                }

                stage ('E2E Tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0'
                            reuseNode true
                        }
                    }
                    environment {
                        NPM_CONFIG_REGISTRY = credentials('npm-registry')
                    }
                    steps {
                        sh '''
                            npm install -g serve
                            serve -s build &
                            sleep 10
                            npx playwright test
                        '''
                    }
                }
            }
        }

        stage ('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            environment {
                NPM_CONFIG_REGISTRY = credentials('npm-registry')
            }
            steps {
                sh '''
                    export NODE_TLS_REJECT_UNAUTHORIZED=0
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "Deploying to Netlify. Site ID: ${NETLIFY_SITE_ID}"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }

        stage ('PROD E2E Tests') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0'
                    reuseNode true
                }
            
            }
            environment {
                CI_ENVIRONMENT_URL = 'https://delightful-melba-e4dc18.netlify.app'
            }

            steps {
                sh '''
                    npx playwright test
                '''
            }

            post {
                always {
                    publishHTML (target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: false,
                        reportDir: 'playwright-report',
                        reportFiles: 'index.html',
                        reportName: "Playwright E2E Test Report"
                        reportTitles: "Test Report"
                        useWrapperFileDirectory: true
                    ])
                }
            }
        }

        /*stage ('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0'
                    reuseNode true
                }
            }
            environment {
                NPM_CONFIG_REGISTRY = credentials('npm-registry')
            }
            steps {
                sh '''
                    npm install -g serve
                    serve -s build &
                    sleep 10
                    npx playwright test
                '''
            }
        }*/
    }
}
