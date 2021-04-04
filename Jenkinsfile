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
            }
        }
        stage('Code Quality Check on SonarQube Server') {
            steps {
                script {
                    def scannerHome = tool 'sonarqube-scanner';
                    withSonarQubeEnv("sonarqube-container") {
                    sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=mv-pass-sonarqube-analysis \
                        -Dsonar.sources=. \
                        -Dsonar.css.node=. \
                        -Dsonar.host.url=http://35.209.18.164:9000 \
                        -Dsonar.login=admin \
                        -Dsonar.password=sonar"
                    }
                }
            }
        }
        stage('Install Dependency Packages') {
            environment {
                PORT = 8082
            }

            steps {
                echo 'install dependencies'
                sh 'npm install'
                echo 'Run Mock/Sanity Tests'
                sh 'npm test'
            }
        }
        stage("Quality Gate Check for Validating Pass/Fail") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        stage('Build the Docker Image with new code changes') {
            steps {
                echo 'Tests passed and ready to build the docker image and push to container registry'
                sh 'gcloud builds submit -t gcr.io/devops-2021-308721/mv-frontend-image:v1.${BUILD_NUMBER} .'
            }
        }
        stage('Deploy the Newly Created Image onto Containers') {
            steps {
                echo 'Get Cluster Credentials'
                sh 'gcloud container clusters get-credentials mv-terradev-cluster --zone us-central1-a --project devops-2021-308721'
                echo 'Deploy the containers with new image'
                sh 'kubectl set image deployment/frontend-deployment terradev-frontend=gcr.io/devops-2021-308721/mv-frontend-image:v1.${BUILD_NUMBER} -n terradev --record'
            }
        }        
    }
}
