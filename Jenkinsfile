pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
               git(
            	url: 'https://github.com/ahmedsaadx/goapp.git',
            	credentialsId: 'token',
            	branch: 'main'
            )
            }
        }
        stage('Build') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                        ls
                        docker login -u ${USERNAME} -p ${PASSWORD}
                        docker build . -t ahmedsaadx0/goapp:${BUILD_NUMBER}
                        docker push ahmedsaadx0/goapp:${BUILD_NUMBER}
                    """
                } 
            }
        }
        stage ("Updating Deployment Manifest") {
            steps {
                script {
                    sh 'sed -i s+docker.io/ahmedsaadx0/goapp+docker.io/ahmedsaadx0/goapp:${BUILD_NUMBER}+g manifests/deployment.yaml'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh """                    
                    cd manifests
                    kubectl apply -f ns.yaml
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                """
            }
        }
    }
}

