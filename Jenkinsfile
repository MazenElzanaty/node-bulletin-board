pipeline {
    agent none

    options { 
        disableConcurrentBuilds() 
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
        stage('Provision Dind Agent') {
            agent {
                kubernetes {
                    defaultContainer 'docker'
                    yamlFile 'dind.yaml'
                }
            }

            stages {
                stage('Change') {
                    steps {
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
        
        stage('Deploy') {
            agent {
                kubernetes {
                    defaultContainer 'helm'
                    yamlFile 'helm.yaml'
                }
            }
            steps {
                container('helm') {
                    sh "helm upgrade --install  node-bulletin-board  charts/node-bulletin-board/ -f charts/node-bulletin-board/values.yaml -n demo --set image.tag=$IMAGE_TAG"
                }
            }
        }
    }
}