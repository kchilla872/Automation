pipeline {
    agent any

    environment {
        PYTHON_PATH = 'C:\\Program Files\\Python313'
    }

    stages {
        stage('Check Python') {
            steps {
                bat """
                    @echo off
                    set "PATH=${env.PYTHON_PATH};%PATH%"
                    echo Checking Python installation...
                    where python
                    python --version
                    echo Python location confirmed!
                """
            }
        }

        stage('Install Dependencies') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    bat """
                        @echo off
                        set "PATH=${env.PYTHON_PATH};%PATH%"
                        echo Starting dependency installation...
                        python -m venv venv
                        echo Virtual environment created
                        call venv\\Scripts\\activate
                        python -m pip install --upgrade pip
                        echo Pip upgraded
                        pip install -r requirements.txt
                        echo Requirements installed
                        playwright install
                        echo Playwright installed
                    """
                }
            }
        }

        stage('Run Tests') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    bat """
                        @echo off
                        set "PATH=${env.PYTHON_PATH};%PATH%"
                        call venv\\Scripts\\activate
                        pytest homePage.py -v --junit-xml=test-results.xml --html=playwright-report\\report.html --self-contained-html
                    """
                }
            }
        }
    }

    post {
        always {
            script {
                if (fileExists('test-results.xml')) {
                    junit 'test-results.xml'
                } else {
                    echo 'No JUnit XML test results found to archive'
                }

                if (fileExists('playwright-report/report.html')) {
                    archiveArtifacts artifacts: 'playwright-report/report.html', fingerprint: true
                } else {
                    echo 'No HTML test report found to archive'
                }
            }
            cleanWs()
        }
    }
}
