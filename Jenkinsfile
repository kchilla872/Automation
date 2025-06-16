pipeline {
    agent any

    stages {
        stage('Echo Check') {
            steps {
                bat '''
                    @echo on
                    echo Hello from Jenkins
                    echo Running as: %USERNAME%
                    echo Current directory: %CD%
                '''
            }
        }

        stage('Python Version') {
            steps {
                bat '"C:\\Program Files\\Python313\\python.exe" --version'
            }
        }

        stage('Pip Version') {
            steps {
                bat '"C:\\Users\\karthik.chillara\\AppData\\Roaming\\Python\\Python313\\Scripts\\pip.exe" --version'
            }
        }

        stage('Where Python') {
            steps {
                bat 'where python'
            }
        }
    }

    post {
        always {
            echo "Test pipeline completed"
        }
    }
}
