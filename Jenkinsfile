pipeline {
    agent any

    environment {
        PYTHON_PATH = 'C:\\Program Files\\Python313'
    }

    stages {
        stage('Python Setup') {
            steps {
                bat """
                    @echo off
                    set "PATH=${env.PYTHON_PATH};%PATH%"
                    echo ===== PATH Check =====
                    echo %PATH%
                    echo ===== where python =====
                    where python
                    echo ===== python version =====
                    python --version
                    echo ===== finished checking python =====
                """
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                bat """
                    @echo off
                    set "PATH=${env.PYTHON_PATH};%PATH%"
                    echo Setting up virtual environment...
                    python -m venv venv
                    echo Virtual environment created successfully
                """
            }
        }

        stage('Install Dependencies') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    bat """
                        @echo off
                        set "PATH=${env.PYTHON_PATH};%PATH%"
                        call venv\\Scripts\\activate
                        echo Virtual environment activated
                        python -m pip install --upgrade pip
                        pip install -r requirements.txt
                        playwright install
                        echo Dependencies installed successfully
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
                        echo Running tests...
                        pytest homePage.py -v --junit-xml=test-results.xml --html=playwright-report\\report.html --self-contained-html
                        echo Tests completed
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
                    echo '❌ No HTML test report found to archive'
                }
            }
            cleanWs()
        }
    }
}
