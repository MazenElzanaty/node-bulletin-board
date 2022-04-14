pipeline {
    agent {
        kubernetes {
            label 'dind'
            defaultContainer 'docker'
            yaml """
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: jenkins
spec:
  containers:
    - name: docker
      image: docker:latest
      command:
        - /bin/cat
      tty: true
      volumeMounts:
        - name: dind-certs
          mountPath: /certs
      env:
        - name: DOCKER_TLS_CERTDIR
          value: "/certs"
        - name: DOCKER_CERT_PATH
          value: "/certs"
        - name: DOCKER_TLS_VERIFY
          value: "1"
        - name: DOCKER_HOST
          value: "tcp://localhost:2376"
    - name: dind
      image: docker:dind
      securityContext:
        privileged: true
      env:
        - name: DOCKER_TLS_CERTDIR
          value: "/certs"
      volumeMounts:
        - name: dind-storage
          mountPath: /var/lib/docker
        - name: dind-certs
          mountPath: /certs/client
  volumes:
    - name: dind-storage
      emptyDir: {}
    - name: dind-certs
      emptyDir: {}
"""
        }

    }
    environment {
        CONTAINR_REPO  = credentials('CONTAINR_REPO')
        CONTAINR_USER  = credentials('CONTAINR_USER')
        CONTAINR_PASS  = credentials('CONTAINR_PASS')
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
                sh 'docker build -t ${env.CONTAINR_REPO}:${env.BUILD_NUMBER} .'
                echo "Built Docker Image"
            }
        }
        stage('Push') {
            steps {
                sh 'docker login -u ${env.CONTAINR_USER} -p ${env.CONTAINR_PASS}'
                sh 'docker push ${env.CONTAINR_REPO}:${env.BUILD_NUMBER}'
                echo "Pushed Docker Image"
            }
        }
    }
}