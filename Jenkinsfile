pipeline {
  agent slave1

  stages {
    stage('Build Docker image') {
      steps {
        sh 'docker build -t my-docker-image .'
      }
    }
  }
}
