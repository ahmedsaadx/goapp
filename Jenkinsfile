pipeline {
agent{
    kubernetes{
        label 'slave'
    }
}
    environment {
        ZONE = "us-east1-b"
        PROJECT_ID = "saad-375811"
    }

    stages{
        
        stage("git checkout"){
            steps{
                script{
        git(
            url: 'git@github.com:ahmedsaadx/go-app.git',
            credentialsId: 'git-token',
            branch: 'main'
            )
                }
            }
        }
        
        stage("Building Application Docker Image"){
            steps{
                script{
                    sh 'gcloud auth configure-docker asia-south1-docker.pkg.dev'
                    sh 'docker build . -t gcr.io/saad275811/hello-app:${BUILD_NUMBER}'
                    }
                }
            }
        stage("Pushing Application Docker Image to Google Artifact Registry"){
            steps{
                script{
                    sh 'docker push gcr.io/saad275811/hello-app:${BUILD_NUMBER}'
        }}}
        stage ("Updating Deployment Manifest") {
            steps {
                script {
                    sh 'sed -i s+gcr.io/saad375811/hello-app:1+hello-app:v1gcr.io/saad375811/hello-app:${BUILD_NUMBER}'+g  manifests/deployment.yaml'
                }
            }
        }
        stage("Application Deployment on Google Kubernetes Engine"){
            steps{
                script{
                    sh "gcloud container clusters get-credentials app-cluster --zone ${env.ZONE} --project ${env.PROJECT_ID}"
                    sh 'kubectl apply -f manifests/.'
                }
            }
        }
    }
    }
