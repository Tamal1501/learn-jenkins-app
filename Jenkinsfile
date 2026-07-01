pipeline {
    agent any

    environment {
        NETLIFY_PROJECT_ID = '81dfefce-3ae9-4344-94ad-b9659299879d'
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
                    echo "Deploying to Netlify. Project ID: ${NETLIFY_PROJECT_ID}"
                '''
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
