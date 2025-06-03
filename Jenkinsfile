pipeline {
    agent any

    stages {
        stage('Start') {
            steps {
                echo 'Start Lab_7'
            }
        }

        stage('Auth to HCP') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'hcp',
                    usernameVariable: 'Hlkj6rvPVSIyUKjgqswOXS4iUpsIC9l5',
                    passwordVariable: 'x5ghgDc3WPhn3BwtupY9XRCx095dR5H6oKYnxNSs7WvrX4-tGToebNeFQ27B3aa_')]) {
                    sh '''
                        hcp auth login --client-id $MY_ID --client-secret $MY_SECRET
                    '''
                }
            }
        }

        stage('Init HCP') {
            steps {
                sh 'hcp profile set vault-secrets/app lab-7'
            }
        }

        stage('Build nginx/custom') {
            steps {
                sh 'docker build -t nginx/custom:latest .'
            }
        }

        stage('Deploy nginx/custom') {
            steps {
                sh 'docker run -d -p 80:80 nginx/custom:latest'
            }
        }

        stage('Finish') {
            steps {
                echo 'Finish Lab_7'
            }
        }
    }

    post {
        always {
            script {
                // За потреби можна зберігати URL вебхука або інші секрети, якщо потрібні
                env.webhookUrl = sh(
                    script: 'hcp vault-secrets secrets open slack_webhook --format=json | jq -r .static_version.value',
                    returnStdout: true
                ).trim()
            }
        }

        success {
            slackSend(
                channel: '#назва_твого_каналу',
                message: "✅ Build success!",
                color: "good"
            )
        }

        failure {
            slackSend(
                channel: '#назва_твого_каналу',
                message: "❌ Build failed!",
                color: "danger"
            )
        }
    }
}

