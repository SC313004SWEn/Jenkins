pipeline {
    agent any

    stages {
        stage('Cleanup') {
            steps {
                echo 'Cleaning up old results...'
                // Use || true to ensure the build doesn't fail if the folder doesn't exist yet
                sh 'rm -rf results || true'
                sh 'mkdir -p results'
            }
        }

        stage('Verify Robot') {
            steps {
                sh 'python3 --version'
                sh 'robot --version'
            }
        }

        stage('Run Robot Tests') {
            steps {
                script {
                    sh 'robot --outputdir results *.robot || true'
                }
            }
        }
    }

    post {
        always {
            // Check if output.xml was actually created before trying to publish
            script {
                def exists = fileExists 'results/output.xml'
                if (exists) {
                    step([$class: 'RobotPublisher',
                        outputPath: 'results',
                        outputFileName: 'output.xml',
                        reportFileName: 'report.html',
                        logFileName: 'log.html',
                        otherFiles: ''
                    ])
                } else {
                    echo "ERROR: results/output.xml not found! Robot might have failed to find any .robot files."
                }
            }
        }
    }
}