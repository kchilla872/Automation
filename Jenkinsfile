pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                bat 'python -m venv venv'
                bat 'python -m pip install --upgrade pip'
                bat 'call venv\\Scripts\\activate && pip install -r requirements.txt'
                bat 'call venv\\Scripts\\activate && playwright install'
            }
        }
        stage('Run Tests') {
            steps {
                bat 'call venv\\Scripts\\activate && pytest homePage.py -v --junit-xml=test-results.xml'
            }
        }
    }
    post {
        always {
            // Only archive test results if they exist
            script {
                if (fileExists('test-results.xml')) {
                    junit 'test-results.xml'
                } else {
                    echo 'No test results found to archive'
                }
            }
            cleanWs()
        }
    }
}