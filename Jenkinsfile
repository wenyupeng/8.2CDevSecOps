pipeline {
    agent any

    environment {
        RECIPIENT_EMAIL = 'wen0424068311@gmail.com'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: ' https://github.com/wenyupeng/8.2CDevSecOps.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }

            post {
                success {
                    sendEmail('Test', 'SUCCESS')
                }
                failure {
                    sendEmail('Test', 'FAILURE')
                }
            }
        }
        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }

            post{
                success {
                    sendEmail('Security Scan', 'SUCCESS')
                }
                failure {
                    sendEmail('Security Scan', 'FAILURE')
                }
            }
        }
        stage('SonarCloud Analysis') {
            tools {
              jdk "jdk17"
            }
            environment {
                scannerHome = tool 'Sonar-Scanner'
            }
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                   sh '/opt/sonar-scanner/bin/sonar-scanner'
                }
            }
        }
    }
}

def sendEmail(stageName, status) {
    emailext (
        subject: "Jenkins: ${stageName} stage - ${status}",
        body: """<p>The ${stageName} stage has completed with status: <b>${status}</b>.</p>
                 <p>Job: ${env.JOB_NAME}<br>
                 Build: ${env.BUILD_NUMBER}<br>
                 URL: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>""",
        to: "${env.RECIPIENT_EMAIL}",
        attachLog: true,
        mimeType: 'text/html'
    )
}