pipeline {
    agent any
    stages {
        stage('Trigger Code Pipeline from GitHub') {
            steps {
                echo 'Verifying Jenkins trigger of build extract from Git Repo...'
                git branch: 'master',
                    url: 'https://github.com/MaryLV/frontend-mar15.git'
                echo 'Did we get the source code from Git Repo?'
                sh 'ls -al'
            }
        }
        stage('Validate the Env') {
            steps {
                echo 'Workspace and Version Validation...'
                sh 'echo $WORKSPACE'
                sh 'gcloud version'
                sh 'nodejs -v'
                sh 'npm -v'
                sh 'java --version'
            }
        }
    }
}
