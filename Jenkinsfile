pipeline {
    agent any
    environment {
        EMAIL_RECIPIENT = 'rohanraoa@gmail.com'
        TEST_STAGE_STATUS = 'NOT_RUN'
        SECURITY_STAGE_STATUS = 'NOT_RUN'
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
                    env.TEST_STAGE_STATUS = (testExitCode == 0) ? 'SUCCESS' : 'FAILURE'
                    echo "Run Tests stage status: ${env.TEST_STAGE_STATUS}"
                }
            }
            post {
                always {
                    emailext(
                        to: "${EMAIL_RECIPIENT}",
                        subject: "Jenkins ${env.JOB_NAME} #${env.BUILD_NUMBER} - Run Tests: ${env.TEST_STAGE_STATUS}",
                        body: """Stage: Run Tests
Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Result: ${env.TEST_STAGE_STATUS}
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
                    env.SECURITY_STAGE_STATUS = (auditExitCode == 0) ? 'SUCCESS' : 'FAILURE'
                    echo "Security Scan stage status: ${env.SECURITY_STAGE_STATUS}"
                }
            }
            post {
                always {
                    emailext(
                        to: "${EMAIL_RECIPIENT}",
                        subject: "Jenkins ${env.JOB_NAME} #${env.BUILD_NUMBER} - Security Scan: ${env.SECURITY_STAGE_STATUS}",
                        body: """Stage: NPM Audit (Security Scan)
Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Result: ${env.SECURITY_STAGE_STATUS}
URL: ${env.BUILD_URL}""",
                        attachLog: true
                    )
                }
            }
        }
    }
}
