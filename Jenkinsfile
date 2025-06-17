pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building Python environment and installing dependencies...'
                bat '''
                    cd "C:\\Users\\karthik.chillara\\PycharmProjects\\Amazondemo0616"
                    cmd /c "call venv\\Scripts\\activate.bat && pip install -r requirements.txt && playwright install chromium --with-deps"
                    echo Build completed successfully
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running Playwright tests...'
                bat '''
                    cd "C:\\Users\\karthik.chillara\\PycharmProjects\\Amazondemo0616"
                    cmd /c "call venv\\Scripts\\activate.bat && pytest test_homePage.py -v --html=report.html --self-contained-html"
                    echo Tests completed
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Archiving test results...'
                script {
                    if (fileExists('C:\\Users\\karthik.chillara\\PycharmProjects\\Amazondemo0616\\report.html')) {
                        archiveArtifacts artifacts: 'C:\\Users\\karthik.chillara\\PycharmProjects\\Amazondemo0616\\report.html', allowEmptyArchive: true
                    } else {
                        echo "⚠️ No test report found to archive"
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning workspace..."
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully!"
        }
    }
}
