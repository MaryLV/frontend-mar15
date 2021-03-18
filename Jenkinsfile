pipeline {
  agent any
  stages {
      stage('Stage 1') {
          steps{
              echo 'Retrieving source code'
              git 'https://github.com/MaryLV/frontend-mar15.git'
              echo 'Source code is here'
              sh 'ls -a'
          }
      }
      stage('Stage 2'){
          steps {
              echo 'workspace and versions'
              sh 'echo $WORKSPACE'
              sh 'gcloud version'
              sh 'nodejs -v'
              sh 'npm -v'
          }
      }
      stage('Stage 3'){
          environment {
              PORT = 8081
          }
          steps {
              echo 'install dependencies'
              sh 'npm install'
              echo 'Run tests'
              sh 'npm test'
          }
      }
      stage('Stage 4') {
          steps {
              echo "build id = ${env.BUILD_ID}"
              echo 'Tests passed'
              sh "gcloud builds submit -t gcr.io/mar-roidtc302/external-container:v2.${env.BUILD_ID} ."
          }
        }
      stage('Stage 5') {
          steps {
              echo 'Get cluster credentials'
              sh 'gcloud container clusters get-credentials cluster-2 --zone us-central1-c --project mar-roidtc302'
              echo 'Update the image'
              echo "gcr.io/mar-roidtc302/external-container:v2.${env.BUILD_ID}"
              sh "kubectl set image deployment/events-external events-external=gcr.io/mar-roidtc302/external-container:v2.${env.BUILD_ID}"
          }
      }
}
}
