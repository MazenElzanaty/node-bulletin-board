pipeline {
    agent {
        kubernetes {
            label 'dind'
            defaultContainer 'docker'
            yamlFile 'dind.yaml'
            containerTemplate {
                name 'helm'
                image 'alpine/helm'
                ttyEnabled true
                command 'cat'
            }
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
        stage('Change') {
            steps {
                sh 'sh change.sh'
                echo "Applied changes"
            }
        }
        // stage('Build and Push') {
        //     steps {
        //         sh 'docker build -t $CONTAINR_REPO:$IMAGE_TAG .'
        //         echo "Built Docker Image"
        //         sh 'docker login -u $CONTAINR_USER -p $CONTAINR_PASS'
        //         sh 'docker push $CONTAINR_REPO:$IMAGE_TAG'
        //         echo "Pushed Docker Image"
        //     }
        // }
        stage('Deploy') {     
            steps {
                container('helm') {
                    //sh "helm upgrade --install  node-bulletin-board  charts/node-bulletin-board/ -f charts/node-bulletin-board/values.yaml -set image.tag=$IMAGE_TAG -n demo --wait"
                    echo "Hello"
                    sh "helm --version"
                }
            }
        }
    }
}