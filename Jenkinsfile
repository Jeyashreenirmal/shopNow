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
                    docker login --username AWS --password-stdin 975050024946.dkr.ecr.us-east-1.amazonaws.com
                    '''
                }
            }
        }

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

        stage('Build & Push Backend') {
            steps {
                dir('backend') {
                    sh '''
                    docker build -t jsree-shopnow-b:backend .
                    docker tag jsree-shopnow-b:backend 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-b:backend-v1
                    docker push 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-b:backend-v1
                    '''
                }
            }
        }

        stage('Build & Push Admin') {
            steps {
                dir('admin') {
                    sh '''
                    docker build -t jsree-shopnow-a:admin .
                    docker tag jsree-shopnow-a:admin 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-a:admin
                    docker push 975050024946.dkr.ecr.us-east-1.amazonaws.com/jsree-shopnow-a:admin
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'All services built and pushed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}