pipeline {
    agent any
    environment {
        EMAIL_RECIPIENT = 'rohanraoa@gmail.com'
    }

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
                script {
                    def testExitCode = dir('nodejs-goof') {
                        sh(script: 'npm test', returnStatus: true)
                    }
                    if (testExitCode != 0) {
                        unstable('Run Tests failed')
                    }
                }
            }
            post {
                always {
                    emailext(
                        to: "${EMAIL_RECIPIENT}",
                        subject: "Jenkins ${env.JOB_NAME} #${env.BUILD_NUMBER} - Run Tests: ${currentBuild.currentResult}",
                        body: """Stage: Run Tests
Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Result: ${currentBuild.currentResult}
URL: ${env.BUILD_URL}""",
                        attachLog: true
                    )
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
                script {
                    def auditExitCode = dir('nodejs-goof') {
                        sh(script: 'npm audit', returnStatus: true)
                    }
                    if (auditExitCode != 0) {
                        unstable('Security Scan found issues')
                    }
                }
            }
            post {
                always {
                    emailext(
                        to: "${EMAIL_RECIPIENT}",
                        subject: "Jenkins ${env.JOB_NAME} #${env.BUILD_NUMBER} - Security Scan: ${currentBuild.currentResult}",
                        body: """Stage: NPM Audit (Security Scan)
Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Result: ${currentBuild.currentResult}
URL: ${env.BUILD_URL}""",
                        attachLog: true
                    )
                }
            }
        }
    }
}
