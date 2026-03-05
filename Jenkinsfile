pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main',
            url: 'https://github.com/Jeyashreenirmal/shopNow.git',
            credentialsId: 'github-tokenjsree'
      }
    }
     stage('Login to AWS ECR') {
      steps {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: 'aws-credentials'
        ]]) {
          sh '''
            aws ecr get-login-password --region us-east-1 | \
            docker login --username AWS --password-stdin \
            975050024946.dkr.ecr.us-east-1.amazonaws.com
          '''
        }
      }
    }

    // ================= FRONTEND =================
    stage('Build & Push Frontend') {
      steps {
        dir('frontend') {
          sh '''
            docker build -t jsree-shopnow-f:frontend .
            docker tag jsree-shopnow-f:frontend 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-f:frontend
            docker push 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-f:frontend
          '''
        }
      }
    }
    stage('Build & Push backend') {
      steps {
        dir('backend') {
          sh '''
            docker build -t jsree-shopnow-b:backend-v1 .
            docker tag jsree-shopnow-b:backend-v1 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-b:backend-v1
            docker push 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-b:backend-v1
          '''
        }
      }
    }
    stage('Build & Push Admin Service') {
      steps {
        dir('admin') {
          sh '''
            docker build -t shopnow-jsree-a:admin .
            docker tag shopnow-jsree-a:admin 975050024946.dkr.ecr.us-east-1.amazonaws.com/shopnow-jsree-a:admin
            docker push 975050024946.dkr.ecr.us-east-1.amazonaws.com/shopnow-jsree-a:admin
          '''
        }
      }
    }
     post {
    success {
      echo '✅ All services built and pushed successfully'
    }
    failure {
      echo '❌ Pipeline failed'
    }
  }
}