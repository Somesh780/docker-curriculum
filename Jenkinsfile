pipeline {
  agent {label 'slave1'}
  environment {
    AWS_ACCOUNT_ID="772569463424"
    AWS_DEFAULT_REGION="ap-south-1"
    IMAGE_REPO_NAME="myapp"
    IMAGE_TAG="latest"
    REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
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
        //withCredentials([string(credentialsId: 'AWS_EXECUTION_ROL_SECRET', variable: 'AWS_ECS_EXECUTION_ROL'),string(credentialsId: 'AWS_REPOSITORY_URL_SECRET', variable: 'AWS_ECR_URL')]) {
          script {
            updateContainerDefinitionJsonWithImageVersion()
            // sh("/usr/local/bin/aws ecs register-task-definition --region ${AWS_DEFAULT_REGION} --family ${AWS_ECS_TASK_DEFINITION} --execution-role-arn ${AWS_ECS_EXECUTION_ROL} --requires-compatibilities ${AWS_ECS_COMPATIBILITY} --network-mode ${AWS_ECS_NETWORK_MODE} --cpu ${AWS_ECS_CPU} --memory ${AWS_ECS_MEMORY} --container-definitions file://${AWS_ECS_TASK_DEFINITION_PATH}")
            def taskRevision = sh(script: "/usr/local/bin/aws ecs describe-task-definition --task-definition ${AWS_ECS_TASK_DEFINITION} | egrep \"revision\" | tr \"/\" \" \" | awk '{print \$2}' | sed 's/\"\$//'", returnStdout: true)
            sh("/usr/local/bin/aws ecs update-service --cluster ${AWS_ECS_CLUSTER} --task-definition ${AWS_ECS_TASK_DEFINITION}")
          }
        }
      }
    }
  
}
