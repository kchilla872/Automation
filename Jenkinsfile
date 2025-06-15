pipeline {
    agent any

    environment {
        PYTHON_PATH = 'C:\\Program Files\\Python313'
        PATH = "${env.PYTHON_PATH};${env.PATH}"
    }

    stages {
        stage('Check Python') {
            steps {
                bat '''
                    echo "Checking Python installation..."
                    echo "PATH: %PATH%"
                    where python
                    python --version
                    echo "Python location confirmed!"
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    bat '''
                        echo "Starting dependency installation..."
                        python -m venv venv
                        echo "Virtual environment created"
                        call venv\\Scripts\\activate
                        echo "Virtual environment activated"
                        python -m pip install --upgrade pip
                        echo "Pip upgraded"
                        pip install -r requirements.txt
                        echo "Requirements installed"
                        playwright install
                        echo "Playwright installed"
                    '''
                }
            }
        }

        stage('Run Tests') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    bat 'call venv\\Scripts\\activate && pytest homePage.py -v --junit-xml=test-results.xml'
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
                    echo 'No test results found to archive'
                }
            }
            cleanWs()
        }
    }
}