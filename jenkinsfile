pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/ganesh-bruno/jenkins-setup.git'
            }
        }

        stage('Setup Node.js') {
            steps {
                tool 'Node.js 18'
            }
        }

        stage('Install Bruno CLI') {
            steps {
                sh 'npm install -g @usebruno/cli'
            }
        }

        stage('Run API Tests') {
            steps {
                sh 'bru run --env ci --reporter-html results.html'
            }
        }

        stage('Archive Test Results') {
            steps {
                archiveArtifacts artifacts: 'results.html', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        failure {
            echo 'API Tests Failed!'
        }
    }
}