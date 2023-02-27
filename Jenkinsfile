pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
               git(
            	url: 'https://github.com/ahmedsaadx/goapp.git',
            	credentialsId: 'git-token',
            	branch: 'main'
            )
            }
        }
        
        stage('Build') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                        ls
                        docker login -u ${USERNAME} -p ${PASSWORD}
                        docker build . -t ahmedsaadx0/goapp
                        docker push ahmedsaadx0/goapp
                    """
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
