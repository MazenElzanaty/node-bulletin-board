pipeline {
    agent {
        kubernetes {
            label 'dind'
            defaultContainer 'docker'
            yaml dind.yaml
        }

    }
    triggers {
        pollSCM('*/1 * * * *')
    } 
    environment {
        CONTAINR_REPO  = credentials('CONTAINR_REPO')
        CONTAINR_USER  = credentials('CONTAINR_USER')
        CONTAINR_PASS  = credentials('CONTAINR_PASS')
        IMAGE_TAG      = "${currentBuild.number}"
    }
    stages {
        stage('Clone') {
            steps {
                checkout scm
            }
        }
        stage('Change') {
            steps {
                sh('ls -lah')
                sh 'sh change.sh'
                echo "Applied changes"
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t $CONTAINR_REPO:$IMAGE_TAG .'
                echo "Built Docker Image"
            }
        }
        stage('Push') {
            steps {
                sh 'docker login -u $CONTAINR_USER -p $CONTAINR_PASS'
                sh 'docker push $CONTAINR_REPO:$IMAGE_TAG'
                echo "Pushed Docker Image"
            }
        }
    }
}