pipeline {
    agent any

    stages {
        stage('Setup and Test') {
            steps {
                script {
                    bat '''
                        cd "C:\\Users\\karthik.chillara\\PycharmProjects\\Amazondemo0616"
                        call venv\\Scripts\\activate
                        pip install -r requirements.txt
                        playwright install chromium --with-deps
                        pytest test_homePage.py -v --html=report.html --self-contained-html
                    '''
                }
            }
        }
    }
}
