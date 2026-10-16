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
                echo 'Installing Python dependencies'

                sh '''
                    python -m venv .venv
                    .venv/bin/pip install -r requirements.txt
                    .venv/bin/python3 -m pip install --upgrade pip
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running automated tests'

                sh '''
                    .venv/bin/python -m pytest

                '''
            }
        }
        stage('Code Inspection') {
            steps {

                withCredentials([
                    string(
                        credentialsId: 'sonar-token',
                        variable: 'SONAR_TOKEN'
                    )
                ]) {

                    sh '''
                        export PATH=/opt/sonar-scanner/bin:$PATH
                        sonar-scanner \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.token=$SONAR_TOKEN
                    '''
                }
            }
        }

        

    }

    
}
