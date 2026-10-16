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

                // Windows uses 'bat' instead of 'sh'
                bat '''
                    python -m venv .venv
                    .venv\\Scripts\\pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running automated tests'

                bat '''
                    .venv\\Scripts\\python -m pytest
                '''
            }
        }

        stage('Code Inspection') {
            steps {
                echo 'Running SonarQube analysis'

                bat '''
                    sonar-scanner
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application'

                bat '''
                    @echo off
                    :: Create deployment target directory
                    if not exist C:\\temp\\student-task-manager mkdir C:\\temp\\student-task-manager

                    :: Copy files (xcopy for directories, copy for files)
                    copy app.py C:\\temp\\student-task-manager\\
                    copy requirements.txt C:\\temp\\student-task-manager\\
                    xcopy /E /I /Y templates C:\\temp\\student-task-manager\\templates
                    xcopy /E /I /Y static C:\\temp\\student-task-manager\\static

                    cd C:\\temp\\student-task-manager

                    :: Create localized isolated venv
                    python -m venv .venv
                    .venv\\Scripts\\pip install -r requirements.txt

                    :: Kill existing running Flask apps bound to port 5000 to avoid conflicts
                    for /f "tokens=5" %%a in ('netstat -aon ^| findstr :5000 ^| findstr LISTENING') do (
                        echo Killing existing process ID %%a
                        taskkill /F /PID %%a
                    )

                    :: Start the application in the background (Windows equivalent of nohup)
                    set PYTHONPATH=.
                    start /B .venv\\Scripts\\python app.py > app.log 2>&1
                '''
            }
        }

    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed - deployment was not completed'
        }
    }
}
