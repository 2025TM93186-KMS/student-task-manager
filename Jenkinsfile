pipeline {
    agent any
    triggers {
        pollSCM('H/2 * * * *')
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code'
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo 'Installing Dependencies'
                bat '''
                    python -m venv .venv
                    .venv\\Scripts\\python -m pip install --upgrade pip
                    .venv\\Scripts\\pip install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                echo 'Running automated test'
                bat '''
                    .venv\\Scripts\\python -m pytest
                '''
            }
        }
    }
}
