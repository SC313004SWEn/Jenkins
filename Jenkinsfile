pipeline {
    agent any

    stages {
        stage('Cleanup') {
            steps {
                sh 'rm -rf results || true'
                sh 'mkdir -p results'
            }
        }

        stage('Verify Robot') {
            steps {
                // Testing robot --version often returns 251 in certain environments
                // Adding || true ensures the pipeline doesn't stop here
                sh 'python3 --version || true'
                sh 'robot --version || true'
            }
        }

        stage('Run Robot Tests') {
            steps {
                script {
                    // Force the path to your tests folder
                    // We use "|| true" to ensure we reach the Post-Actions to see the report
                    sh 'robot --outputdir results tests/ || true'
                }
            }
        }
    }

    post {
        always {
            script {
                // Debug: list exactly what is in the workspace to see if 'tests' folder exists
                sh 'ls -R' 
                
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
                    echo "CRITICAL ERROR: results/output.xml was never created."
                }
            }
        }
    }
}