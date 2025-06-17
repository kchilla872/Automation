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

                timeout(time: 10, unit: 'MINUTES') {
                    bat """
                        @echo off
                        echo ===== Environment Setup =====
                        echo Current user: %USERNAME%
                        echo Current directory: %CD%

                        echo ===== Verifying Python Installation =====
                        if exist "${env.PYTHON_EXE}" (
                            echo ✓ Python executable found
                            "${env.PYTHON_EXE}" --version
                        ) else (
                            echo ❌ ERROR: Python executable not found
                            exit 1
                        )

                        echo ===== Setting up Virtual Environment =====
                        if exist "venv\\\" rmdir /s /q venv
                        "${env.PYTHON_EXE}" -m venv venv
                        call venv\\Scripts\\activate

                        echo ===== Installing Dependencies =====
                        python -m pip install --upgrade pip

                        if exist "requirements.txt" (
                            pip install -r requirements.txt
                        ) else (
                            echo ⚠️  No requirements.txt found
                        )

                        pip install playwright pytest-playwright
                        playwright install

                        echo ✓ Build completed successfully
                    """
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running Playwright tests...'

                timeout(time: 15, unit: 'MINUTES') {
                    bat """
                        @echo off
                        echo ===== Running Tests =====
                        call venv\\Scripts\\activate

                        if exist "homePage.py" (
                            echo Running tests from homePage.py...
                            pytest homePage.py -v --junit-xml=test-results.xml --html=playwright-report\\report.html --self-contained-html
                        ) else (
                            echo Running all Python test files...
                            pytest -v --junit-xml=test-results.xml --html=playwright-report\\report.html --self-contained-html
                        )

                        echo ✓ Tests completed
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Archiving test results and reports...'

                script {
                    // Archive test results
                    if (fileExists('test-results.xml')) {
                        echo "✓ Publishing JUnit test results"
                        junit 'test-results.xml'
                    }

                    // Archive HTML report
                    if (fileExists('playwright-report/report.html')) {
                        echo "✓ Archiving HTML test report"
                        archiveArtifacts artifacts: 'playwright-report/report.html', fingerprint: true
                    }

                    // Archive test artifacts
                    if (fileExists('test-results/')) {
                        echo "✓ Archiving test artifacts"
                        archiveArtifacts artifacts: 'test-results/**/*', allowEmptyArchive: true
                    }

                    echo '✓ Deploy completed successfully'
                }
            }
        }
    }

    post {
        always {
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
            echo "⚠️ Pipeline completed with warnings."
        }
    }
}