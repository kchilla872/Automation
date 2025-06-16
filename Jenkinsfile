pipeline {
    agent any

    environment {
        PYTHON_PATH = 'C:\\Program Files\\Python313'
        PYTHON_EXE = 'C:\\Program Files\\Python313\\python.exe'
        PIP_EXE = 'C:\\Program Files\\Python313\\Scripts\\pip.exe'
    }

    stages {
        stage('Verify Environment') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    bat """
                        @echo off
                        echo ===== Environment Verification =====
                        echo Current user: %USERNAME%
                        echo Current directory: %CD%
                        echo Jenkins workspace: %WORKSPACE%

                        echo ===== Checking Python Installation =====
                        if exist "${env.PYTHON_EXE}" (
                            echo ✓ Python executable found at ${env.PYTHON_EXE}
                        ) else (
                            echo ❌ ERROR: Python executable not found at ${env.PYTHON_EXE}
                            echo Checking common Python locations...
                            if exist "C:\\Python313\\python.exe\" echo Found at C:\\Python313\\python.exe
                            if exist "C:\\Users\\%USERNAME%\\AppData\\Local\\Programs\\Python\\Python313\\python.exe\" echo Found at AppData Local
                            exit 1
                        )

                        if exist "${env.PIP_EXE}" (
                            echo ✓ Pip executable found at ${env.PIP_EXE}
                        ) else (
                            echo ❌ WARNING: Pip executable not found at ${env.PIP_EXE}
                        )

                        echo ===== Directory Contents =====
                        dir "${env.PYTHON_PATH}"

                        echo ===== Environment verification completed =====
                    """
                }
            }
        }

        stage('Python Setup') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    bat """
                        @echo off
                        echo ===== Python Setup Started =====

                        echo Testing Python executable directly...
                        "${env.PYTHON_EXE}" --version
                        if %ERRORLEVEL% neq 0 (
                            echo ❌ ERROR: Failed to execute Python
                            exit 1
                        )

                        echo Testing Python modules...
                        "${env.PYTHON_EXE}" -c "import sys; print('Python executable:', sys.executable)"
                        "${env.PYTHON_EXE}" -c "import sys; print('Python version:', sys.version)"

                        echo Testing pip...
                        "${env.PYTHON_EXE}" -m pip --version
                        if %ERRORLEVEL% neq 0 (
                            echo ❌ ERROR: Pip is not working properly
                            exit 1
                        )

                        echo ✓ Python setup completed successfully
                    """
                }
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    bat """
                        @echo off
                        echo ===== Setting up Virtual Environment =====

                        echo Removing existing venv if present...
                        if exist "venv\" rmdir /s /q venv

                        echo Creating new virtual environment...
                        "${env.PYTHON_EXE}" -m venv venv
                        if %ERRORLEVEL% neq 0 (
                            echo ❌ ERROR: Failed to create virtual environment
                            exit 1
                        )

                        echo Verifying virtual environment...
                        if exist "venv\\Scripts\\python.exe" (
                            echo ✓ Virtual environment created successfully
                        ) else (
                            echo ❌ ERROR: Virtual environment creation failed
                            exit 1
                        )

                        echo Testing virtual environment activation...
                        call venv\\Scripts\\activate
                        python --version
                        echo ✓ Virtual environment setup completed
                    """
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    bat """
                        @echo off
                        echo ===== Installing Dependencies =====

                        call venv\\Scripts\\activate
                        if %ERRORLEVEL% neq 0 (
                            echo ❌ ERROR: Failed to activate virtual environment
                            exit 1
                        )

                        echo Upgrading pip...
                        python -m pip install --upgrade pip
                        if %ERRORLEVEL% neq 0 (
                            echo ❌ ERROR: Failed to upgrade pip
                            exit 1
                        )

                        echo Checking for requirements.txt...
                        if exist "requirements.txt" (
                            echo Installing requirements from requirements.txt...
                            pip install -r requirements.txt
                            if %ERRORLEVEL% neq 0 (
                                echo ❌ ERROR: Failed to install requirements
                                exit 1
                            )
                        ) else (
                            echo ⚠️  WARNING: requirements.txt not found, skipping pip install
                        )

                        echo Installing Playwright...
                        pip install playwright pytest-playwright
                        if %ERRORLEVEL% neq 0 (
                            echo ❌ ERROR: Failed to install Playwright
                            exit 1
                        )

                        echo Installing Playwright browsers...
                        playwright install
                        if %ERRORLEVEL% neq 0 (
                            echo ❌ ERROR: Failed to install Playwright browsers
                            exit 1
                        )

                        echo Listing installed packages...
                        pip list

                        echo ✓ Dependencies installed successfully
                    """
                }
            }
        }

        stage('Run Tests') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    bat """
                        @echo off
                        echo ===== Running Tests =====

                        call venv\\Scripts\\activate
                        if %ERRORLEVEL% neq 0 (
                            echo ❌ ERROR: Failed to activate virtual environment
                            exit 1
                        )

                        echo Checking for test file...
                        if exist "homePage.py" (
                            echo Running tests from homePage.py...
                            pytest homePage.py -v --junit-xml=test-results.xml --html=playwright-report\\report.html --self-contained-html
                        ) else (
                            echo ⚠️  WARNING: homePage.py not found
                            echo Looking for other test files...
                            dir *.py
                            echo Running all Python test files...
                            pytest -v --junit-xml=test-results.xml --html=playwright-report\\report.html --self-contained-html
                        )

                        echo ✓ Tests completed
                    """
                }
            }
        }
    }

    post {
        always {
            script {
                echo "===== Post-build Actions ====="

                // Archive test results
                if (fileExists('test-results.xml')) {
                    echo "✓ Publishing JUnit test results"
                    junit 'test-results.xml'
                } else {
                    echo "⚠️  No JUnit XML test results found to archive"
                }

                // Archive HTML report
                if (fileExists('playwright-report/report.html')) {
                    echo "✓ Archiving HTML test report"
                    archiveArtifacts artifacts: 'playwright-report/report.html', fingerprint: true
                } else {
                    echo "⚠️  No HTML test report found to archive"
                }

                // Archive any screenshots or videos if they exist
                if (fileExists('test-results/')) {
                    echo "✓ Archiving test artifacts"
                    archiveArtifacts artifacts: 'test-results/**/*', allowEmptyArchive: true
                }
            }

            // Clean workspace
            echo "🧹 Cleaning workspace..."
            cleanWs()
        }

        success {
            echo "🎉 Pipeline completed successfully!"
        }

        failure {
            echo "❌ Pipeline failed. Check the logs above for details."
        }

        unstable {
            echo "⚠️  Pipeline completed with warnings."
        }
    }
}