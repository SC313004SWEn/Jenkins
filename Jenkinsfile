pipeline {
    agent any

    environment {
        RESULTS_DIR = "results"
    }

    stages {
        stage('Cleanup') {
            steps {
                echo "Cleaning up old results..."
                sh "rm -rf ${RESULTS_DIR}"
                sh "mkdir -p ${RESULTS_DIR}"
            }
        }

        stage('Build Check') {
            steps {
                echo "Verifying Environment..."
                sh 'python3 --version'
                sh 'robot --version'
                sh 'chromium --version'
            }
        }

        stage('Run Robot Tests') {
            steps {
                sh """
                robot --outputdir ${RESULTS_DIR} \
                      --variable BROWSER:headlesschrome \
                      --variable REMOTE_URL:http://localhost:4444/wd/hub \
                      --settag docker_run \
                      tests/
                """
            }
        }
    }

    post {
        always {
            // Requires "Robot Framework Plugin" installed in Jenkins
            step([$class: 'RobotPublisher',
                outputPath: "${RESULTS_DIR}",
                outputFileName: 'output.xml',
                reportFileName: 'report.html',
                logFileName: 'log.html',
                disableReports: false,
                passThreshold: 100.0,
                unstableThreshold: 80.0
            ])
            
            archiveArtifacts artifacts: "${RESULTS_DIR}/*.*", allowEmptyArchive: true
        }
    }
}