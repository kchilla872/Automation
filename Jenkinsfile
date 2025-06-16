pipeline {
    agent any

    environment {
        PYTHON_EXE = 'C:\\Program Files\\Python313\\python.exe'
        PIP_EXE = 'C:\\Program Files\\Python313\\Scripts\\pip.exe'
    }

    stages {
        stage('Quick Test') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    bat '''
                        @echo off
                        echo ===== Quick Environment Test =====
                        echo Current user: %USERNAME%
                        echo Current directory: %CD%

                        echo ===== Testing Python =====
                        "%PYTHON_EXE%" --version

                        echo ===== Testing Pip =====
                        "%PIP_EXE%" --version

                        echo ===== Test completed successfully =====
                    '''
                }
            }
        }
    }

    post {
        always {
            echo "Test pipeline completed"
        }
    }
}