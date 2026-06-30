pipeline {
    agent any

    stages {

        /*stage('Build') {
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
        }*/

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

        /*stage ('Test') {
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

        stage ('E2E') {
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

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
