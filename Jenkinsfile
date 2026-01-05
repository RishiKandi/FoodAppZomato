pipeline {
  agent any

  environment {
    IMAGE_NAME   = "rishikeshkandi/zomato-app"
    IMAGE_TAG    = "latest"
    AWS_REGION   = "us-east-1"
    CLUSTER_NAME = "zomato-eks-cluster"
  }

  stages {

    stage('Checkout Code') {
      steps {
        git branch: 'main', url: 'https://github.com/RishiKandi/FoodAppZomato.git'
      }
    }

    stage('Install NPM Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
      }
    }

    stage('Push Image to DockerHub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push $IMAGE_NAME:$IMAGE_TAG
          '''
        }
      }
    }

    stage('Deploy to EKS') {
      steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
          sh '''
            aws sts get-caller-identity
            aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME
            kubectl apply -f Kubernetes/deployment.yaml
            kubectl apply -f Kubernetes/service.yaml
            kubectl rollout restart deployment zomato
          '''
        }
      }
    }
  }

  post {
    success {
      echo "✅ CI/CD pipeline executed successfully"
    }
    failure {
      echo "❌ CI/CD pipeline failed"
    }
  }
}
