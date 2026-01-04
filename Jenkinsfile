pipeline {
    agent any

    stages {
     

        stage("Install NPM Dependencies") {
            steps {
                sh 'npm install'
            }
        }

        stage("Build Docker Image") {
            steps {
                sh 'docker build -t zomato-app:1.0 .'
            }
        }

        stage("Tag & Push to DockerHub") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-creds') {
                        sh '''
                          docker tag zomato-app:1.0 rishikeshkandi/zomato-app:1.0
                          docker push rishikeshkandi/zomato-app:1.0
                        '''
                    }
                }
            }
        }
    }
}

