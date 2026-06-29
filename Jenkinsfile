pipeline {
    agent any

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
    }
}
