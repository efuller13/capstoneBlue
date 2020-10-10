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
               sh '''
                            #!/usr/bin/env bash

                            ## Complete the following steps to get Docker running locally

                            # Step 1:
                            # Build image and add a descriptive tag
                            docker build --tag=blueimage .

                            # Step 2: 
                            # List docker images
                            docker image ls

                            # Step 3: 
                            # Run flask app
                            docker run -d -p 8000:80 blueimage
                 '''
            }
        }
        stage('Push blue image') {
            steps {
               sh '''
                            #!/usr/bin/env bash
                            # This file tags and uploads an image to Docker Hub

                            # Assumes that an image is built via `run_docker.sh`

                            # Step 1:
                            # Create dockerpath
                            # dockerpath=<your docker ID/path>
                            dockerpath=blueimage

                            # Step 2:  
                            # Authenticate & tag
                            echo "Docker ID and Image: $dockerpath"
                            docker login --username efuller13 --password-stdin Dizzy*22
                            docker tag blueimage efuller13/blueimage
                            # Step 3:
                            # Push image to a docker repository
                            docker push efuller13/blueimage
                 '''
            }
        }
        stage('Create the kubeconfig file') {
            steps {
              withAWS(region: 'us-east-2', credentials: 'Jenkins'){
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