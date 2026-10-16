pipeline {
	agent any
	triggers{
		pollSCM('H/2 * * * *')
	}
		stages{
			stage('Checkout')
			{
				steps{
					echo 'Checking out source code'
					checkout scm
				}
			}
			stage('Build'){
				steps{
					echo 'Installing Dependencies'
					sh '''
						python3 -m venv .venv
						.venv/bin/pip install -r requirements.txt
						python.exe -m pip install --upgrade pip
					'''
				}
			}
			stage('Test')
			{
				steps{
					echo 'Running automated test'
					sh '''
						.venv/bin/python -m pytest
					'''
				}
			}
		}
}