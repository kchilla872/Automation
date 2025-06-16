pipeline {
    agent any

    environment {
        PYTHON_EXE = 'C:\\Program Files\\Python313\\python.exe'
        PIP_EXE = 'C:\\Users\\karthik.chillara\\AppData\\Roaming\\Python\\Python313\\Scripts\\pip.exe'
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

                        echo ===== Testing Python =====
                        call "%PYTHON_EXE%" --version

                        echo ===== Testing Pip (User Path) =====
                        call "%PIP_EXE%" --version

                        echo ===== End =====
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
