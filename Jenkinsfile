pipeline {
    agent any

    stages {
        stage('w/o Docker') {
            steps {
                sh '''
                    echo "without docker"
                    ls -la
                    touch contanier-no.txt
                '''
            }
        }

        stage('w/ Docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "with docker"
                    ls -la
                    touch contanier-yes.txt
                '''
            }
        }
    }
}
