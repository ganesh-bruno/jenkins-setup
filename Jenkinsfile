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
                script {
                    echo "--- Detailed Node.js Diagnostics (Pre-Patch) ---"
                    tool 'Node.js 18' // Still call tool to ensure installation
                    def nodeHome = tool 'Node.js 18' // Get the base path from the tool step
                    def nodeBinPath = "${nodeHome}/bin"
                    echo "Node.js home detected at: ${nodeHome}"
                    echo "Node.js bin path: ${nodeBinPath}"
                    echo "--- End Detailed Node.js Diagnostics (Pre-Patch) ---"

                    // This is the crucial part: Manually add Node.js bin to PATH
                    withEnv(["PATH+NODE=${nodeBinPath}"]) {
                        echo "--- Verifying PATH after manual injection ---"
                        sh 'echo $PATH' // Verify the PATH now includes Node.js bin
                        echo "--- PATH Verification Complete ---"

                        // Now, these commands should work because 'node' is on PATH
                        sh 'node -v'
                        sh 'npm -v'
                        sh 'npm install -g @usebruno/cli'
                        sh 'bru --version'
                    }
                }
            }
        }

        stage('Run API Tests') {
            steps {
                script {
                    tool 'Node.js 18' // Ensure Node.js is installed if not already
                    def nodeHome = tool 'Node.js 18'
                    def nodeBinPath = "${nodeHome}/bin"

                    // Manually add Node.js bin to PATH for this stage as well
                    withEnv(["PATH+NODE=${nodeBinPath}"]) {
                        sh 'bru run --env ci --reporter-html results.html'
                    }
                }
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