pipeline {
    agent {
        kubernetes
    }
    stages {
        stage('Change') {
            steps {
                sh change.sh
            }
        }
        stage('Build') {
            steps {
                sh 'node --version'
                sh 'npm install'
                
            }
        }
    }
}