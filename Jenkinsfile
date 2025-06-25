// Jenkinsfile
pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/ganesh-bruno/jenkins-setup.git'
            }
        }

        stage('Setup Node.js & Install Bruno CLI') {
            steps {
                // This makes 'Node.js 18' available in the PATH for subsequent commands in this stage
                tool 'Node.js 18'

                // No need for withNode() block, commands here will use the tool defined above
                sh 'node -v'
                sh 'npm -v'
                sh 'npm install -g @usebruno/cli'
                sh 'bru --version'
            }
        }

        stage('Run API Tests') {
            steps {
                // Ensure Node.js 18 is also available in this stage if 'bru' needs it
                // Or you can install Bruno CLI in this stage as well, or define it globally in Jenkins
                tool 'Node.js 18' // Added for this stage, assuming bru needs Node.js

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