pipeline {
    agent any

    stages {
        stage('Linting') {
            steps {
                sh 'tidy -q -e *.html'
            }
        }
        stage('Build blue image') {
            steps {
                sh 'sudo -S ./run_docker.sh'
            }
        }
        stage('Push blue image') {
            steps {
                sh 'sudo -S ./upload_docker.sh'
            }
        }
        stage('Create the kubeconfig file') {
            steps {
              withAWS(region: 'us-east-2', credentials: 'aws_credentials'){
               sh '''
                              aws eks --region us-east-2 update-kubeconfig --name Capstone
                 '''
              }
            }
        }
        stage('Deploy blue container') {
            steps {
                sh 'kubectl apply -f ./blue-controller.json'
            }
        }
        stage('Redirect service to blue container') {
            steps {
                sh 'kubectl apply -f ./blue-green-service.json'
            }
        }        
    }
}