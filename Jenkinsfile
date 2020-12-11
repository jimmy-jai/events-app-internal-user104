// prerequisites: a nodejs app must be deployed inside a kubernetes cluster
// TODO: look for all instances of [] and replace all instances of 
//       the 'variables' with actual values 
// variables:
//      [GITREPO]  https://github.com/jimmy-jai/events-app-external-user104
//      [PROJECTID]  roidtcdec-104
//      [CLUSTER_NAME] kub-cluster-104
//      [ZONE]  us-central1-c
//      //  the following values can be found in the yaml:
//      [DEPLOYMENT_NAME] demo-api
//      [CONTAINER_NAME]  demo-api
// (in the template/spec section of the deployment)

pipeline {
    agent any 
    stages {
        stage('Stage 1') {
            steps {
                echo 'Retrieving source from github' 
                git branch: 'master',
                    url: 'https://github.com/jimmy-jai/events-app-external-user104'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
        }
        stage('Stage 2') {
            steps {
                echo 'workspace and versions' 
                sh 'echo $WORKSPACE'
                sh 'docker --version'
                sh 'gcloud version'
                sh 'nodejs -v'
                sh 'npm -v'
        
            }
        }        
         stage('Stage 3') {
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
                echo 'Tests passed on to build Docker container'
                sh "gcloud builds submit -t gcr.io/roidtcdec-104/internal:v2.${env.BUILD_ID} ."
            }
        }        
         stage('Stage 5') {
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials kub-cluster-104 --zone us-central1-c --project roidtcdec-104'
                echo 'Update the image'
                echo "gcr.io/roidtcdec-104/internal:2.${env.BUILD_ID}"
                sh "kubectl set image deployment/demo-api demo-api=gcr.io/roidtcdec-104/internal:v2.${env.BUILD_ID} --record"
            }
        }        
               

        
    }
}
