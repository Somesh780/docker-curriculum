pipeline {
  agent {label 'slave1'}

  stages {
    stage('Build Docker image') {
      steps {
         dir('flask-app') {
                    // Your steps in this directory
                    sh 'ls -la'
                    sh 'pwd'
                    sh 'docker build -t my-docker-image .'
         }
        
      }
    }
  }
}
