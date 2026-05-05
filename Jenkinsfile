pipeline {
    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/rohanrao-git/8.2CDevSecOp.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('nodejs-goof') {
                    sh 'npm install'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('nodejs-goof') {
                    sh 'npm test || true'
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                dir('nodejs-goof') {
                    sh 'npm run coverage || true'
                }
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                dir('nodejs-goof') {
                    sh 'npm audit || true'
                }
            }
        }
    }
}
