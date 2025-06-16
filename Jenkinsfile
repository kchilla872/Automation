pipeline {
    agent any

    environment {
        PYTHON_EXE = 'C:\\Program Files\\Python313\\python.exe'
        PIP_EXE = 'C:\\Program Files\\Python313\\Scripts\\pip.exe'
    }

    stages {
        stage('Quick Test') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    bat """
                        @echo on
                        echo ===== Quick Environment Test =====
                        echo Current user: %USERNAME%
                        echo Current directory: %CD%

                        echo ===== Checking Python Path =====
                        where python
                        dir "C:\\Program Files\\Python313"

                        echo ===== Checking Pip Path =====
                        where pip
                        dir "C:\\Program Files\\Python313\\Scripts"

                        echo ===== Testing Python =====
                        call "%PYTHON_EXE%" --version || echo Python check failed

                        echo ===== Testing Pip =====
                        call "%PIP_EXE%" --version || echo Pip check failed

                        echo ===== Test completed successfully =====
                    """
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
