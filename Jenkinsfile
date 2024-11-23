pipeline {
    agent any
    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/chris-kyle2/jenkins-project-testing.git', branch: 'main'
                sh "ls -ltr"
            }
        }

        stage('Setup') {
            steps {
                sh '''
                # Ensure python3-venv is installed
                sudo apt update
                sudo apt install -y python3-venv
                
                # Create a virtual environment
                python3 -m venv venv
                
                # Activate the virtual environment and install dependencies
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                # Activate the virtual environment
                . venv/bin/activate
                
                # Run tests
                pytest
                '''
            }
        }
    }
}
