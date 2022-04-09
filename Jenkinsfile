pipeline {

  environment {
    PROJECT = "srinag"
    APP_NAME = "recomendation"
    FE_SVC_NAME = "${APP_NAME}- recomendation"
    CLUSTER = "hipstar"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
     inheritFrom 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  # serviceAccountName: cd-jenkins
  containers:
  - name: golang
    image: golang:1.10
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""
}
  }
  stages {
    stage('Test') {
      steps {
        container('golang') {
          sh """
            ln -s `pwd`
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
        }
      }
    }
    stage('Deploy Dev') {
      steps {
        container('kubectl') {
          
          sh "gcloud auth list"

          sh "gcloud container clusters get-credentials hipstar --zone us-central1-c --project srinag"
          sh "kubectl apply -f recomendationservice.yaml"
        }
      }
    }
  }
}
