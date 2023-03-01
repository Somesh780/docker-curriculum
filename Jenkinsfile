pipeline {
  agent {label 'slave1'}
  environment {
    AWS_ACCOUNT_ID="772569463424"
    AWS_DEFAULT_REGION="ap-south-1"
    IMAGE_REPO_NAME="myapp"
    IMAGE_TAG="latest"
    REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    AWS_ECS_TASK_DEFINITION = "my-td"
    AWS_ECS_CLUSTER = "my-cluster"
    AWS_ECS_SERVICE = ""

  }

  stages {
    stage('Logging into AWS ECR') {
      steps {
          script {
          sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
          }

      }
    }

    stage('Build Docker image') {
    steps {
        dir('flask-app') {
                  // Your steps in this directory
                  sh 'ls -la'
                  sh 'pwd'
                  sh 'docker build -t ${IMAGE_REPO_NAME}:${IMAGE_TAG} .'
        }
      
    }
  
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
      steps{  
          script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
          }
        
      }
    }
    stage('Deploy in ECS') {
      steps {
           sh "aws ecs update-service --cluster my-cluster --service my-service --force-new-deployment"
        
      }
    }

  }
}
