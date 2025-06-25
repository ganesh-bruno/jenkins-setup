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
                script { // Using a script block for more complex Groovy logic and diagnostics
                    echo "--- Detailed Node.js Diagnostics ---"

                    // 1. Force the tool provisioning
                    echo "Attempting to provision Node.js tool 'Node.js 18'..."
                    // The 'tool' step makes the specified Node.js version available on the PATH
                    tool 'Node.js 18'

                    // 2. Locate where Jenkins *thinks* Node.js is installed
                    // The 'tool' step (when executed) returns the path to the installed tool's home directory.
                    def nodeHome = tool 'Node.js 18'
                    echo "Jenkins reports Node.js 'Node.js 18' is installed at: ${nodeHome}"

                    // 3. Construct the expected 'bin' path where 'node' and 'npm' executables reside
                    def nodeBinPath = "${nodeHome}/bin"
                    echo "Expected Node.js bin directory: ${nodeBinPath}"

                    // 4. Check if the 'bin' directory actually exists on the filesystem
                    sh "test -d ${nodeBinPath} && echo 'Node.js bin directory exists.' || echo 'Node.js bin directory DOES NOT exist.'"

                    // 5. List the contents of the 'bin' directory to see 'node', 'npm', etc.
                    echo "Listing contents of expected Node.js bin directory:"
                    sh "ls -l ${nodeBinPath}"

                    // 6. Check if the 'node' executable file exists and if it has execute permissions
                    sh "test -f ${nodeBinPath}/node && echo 'Node executable file found.' || echo 'Node executable file NOT found at expected path.'"
                    sh "test -x ${nodeBinPath}/node && echo 'Node executable is runnable (has execute permissions).' || echo 'Node executable is NOT runnable (permissions issue?).'"

                    // 7. Print the current PATH environment variable to verify 'nodeBinPath' is included
                    echo "Current PATH environment variable (should include Node.js bin):"
                    sh 'echo $PATH'

                    // 8. Attempt to run 'node' directly using its absolute path, bypassing the PATH lookup
                    echo "Attempting to run 'node' directly via its full, absolute path:"
                    sh "${nodeBinPath}/node -v || echo 'Failed to run node via full path. This indicates a problem with the Node.js installation itself.'"

                    echo "--- End Detailed Node.js Diagnostics ---"

                    // If the above diagnostics passed (especially the direct path call),
                    // then the PATH issue is more subtle, and the following commands *should* work.
                    // If the direct path call failed, then the Node.js installation is fundamentally broken.

                    // Original commands
                    sh 'node -v'
                    sh 'npm -v'
                    sh 'npm install -g @usebruno/cli'
                    sh 'bru --version'
                }
            }
        }

        stage('Run API Tests') {
            steps {
                // Ensure Node.js 18 is also available in this stage if 'bru' needs it
                tool 'Node.js 18'

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