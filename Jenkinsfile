pipeline {
  agent any
   environment {
	    DOCKERHUB_CREDENTIALS = credentials('RK-Docker')
    }

    stages {
        stage('checkout code ') {
            steps {
                checkoutgit branch: 'main', credentialsId: 'RK-Git', url: 'https://github.com/rutukeshkoli/userproj.git'
            }
        }
        stage('docker build') {
            steps {
                script {
                    sh '''
                        cd userproj
                        docker build -t rutukesh/usermgmnt:v0.0.$BUILD_ID . --no-cache'''
                }
            }
        }
        stage('pushing image to registry') {
            steps {
                script {
                    sh ''' docker push rutukesh/usermgmnt:v0.0.$BUILD_ID 
                           docker rmi  rutukesh/usermgmnt:v0.0.$BUILD_ID '''
                }
            }
        }
        stage('deploy app') {
            steps {
                script {
                    sh ''' 
                    cd  userproj
                    kubectl config use-context DEV
                    kubectl apply -f deployment.yaml
                    kubectl set image deployment/=usermgmtback usermgmtback=rutukesh/usermgmnt:v0.0.$BUILD_ID  -n userapp --record=true'''
                    
                }
            }
        }
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
    }
}
