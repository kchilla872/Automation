pipeline {
    agent any

    environment {
        PYTHON_PATH = 'C:\\Program Files\\Python313'
        PYTHON_EXE = 'C:\\Program Files\\Python313\\python.exe'
        PIP_EXE = 'C:\\Program Files\\Python313\\Scripts\\pip.exe'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building Python environment and installing dependencies...'

                timeout(time: 8, unit: 'MINUTES') {
                    bat '''
                        @echo off
                        echo ===== Environment Setup =====

                        echo ===== Verifying Python Installation =====
                        if not exist "%PYTHON_EXE%" (
                            echo ERROR: Python executable not found at %PYTHON_EXE%
                            exit /b 1
                        )
                        echo Python found, checking version...
                        "%PYTHON_EXE%" --version

                        echo ===== Setting up Virtual Environment =====
                        if exist "venv\" rmdir /s /q venv
                        "%PYTHON_EXE%" -m venv venv
                        if %ERRORLEVEL% neq 0 (
                            echo ERROR: Failed to create virtual environment
                            exit /b 1
                        )

                        echo ===== Activating Virtual Environment =====
                        call venv\\Scripts\\activate.bat
                        if %ERRORLEVEL% neq 0 (
                            echo ERROR: Failed to activate virtual environment
                            exit /b 1
                        )

                        echo ===== Installing Dependencies =====
                        python -m pip install --upgrade pip --quiet

                        if exist "requirements.txt" (
                            echo Installing from requirements.txt...
                            pip install -r requirements.txt --quiet
                        ) else (
                            echo No requirements.txt found, skipping...
                        )

                        echo Installing Playwright and pytest...
                        pip install playwright pytest-playwright pytest-html --quiet
                        if %ERRORLEVEL% neq 0 (
                            echo ERROR: Failed to install Playwright packages
                            exit /b 1
                        )

                        echo Installing Playwright browsers...
                        playwright install chromium --with-deps
                        if %ERRORLEVEL% neq 0 (
                            echo ERROR: Failed to install Playwright browsers
                            exit /b 1
                        )

                        echo Build completed successfully
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running Playwright tests...'

                timeout(time: 10, unit: 'MINUTES') {
                    bat '''
                        @echo off
                        echo ===== Running Tests =====
                        call venv\\Scripts\\activate.bat

                        if exist "homePage.py" (
                            echo Running tests from homePage.py...
                            pytest homePage.py -v --junit-xml=test-results.xml --html=report.html --self-contained-html
                        ) else (
                            echo Looking for test files...
                            dir *.py /b
                            echo Running all Python test files...
                            pytest -v --junit-xml=test-results.xml --html=report.html --self-contained-html
                        )

                        echo Tests completed
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Archiving test results and reports...'

                script {
                    try {
                        // Archive test results
                        if (fileExists('test-results.xml')) {
                            echo "Publishing JUnit test results"
                            junit 'test-results.xml'
                        } else {
                            echo "No JUnit XML results found"
                        }

                        // Archive HTML report
                        if (fileExists('report.html')) {
                            echo "Archiving HTML test report"
                            archiveArtifacts artifacts: 'report.html', fingerprint: true
                        } else {
                            echo "No HTML report found"
                        }

                        // Archive any screenshots or videos
                        if (fileExists('test-results/')) {
                            echo "Archiving test artifacts"
                            archiveArtifacts artifacts: 'test-results/**/*', allowEmptyArchive: true
                        }

                        echo 'Deploy completed successfully'
                    } catch (Exception e) {
                        echo "Warning: Some artifacts could not be archived: ${e.getMessage()}"
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

        failure {
            echo "Pipeline failed. Check the logs above for details."
        }

        unstable {
            echo "Pipeline completed with warnings."
        }
    }
}