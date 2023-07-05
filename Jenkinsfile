pipeline {
  agent {
    docker {
      image 'node:latest'
    }
  }

  stages {
    stage('Build') {
      steps {
        sh 'npm config get cache'
        sh 'chown -R 997:994 "/.npm"'
        sh 'npm install'
        sh 'npm run build'
      }
    }
    
    stage('Test') {
      steps {
        sh 'npm test'
      }
    }
    
    stage('Publish') {
      steps {
        script {
          def componentsPath = 'build'
          def nexusUrl = 'http://localhost:8081'
          def nexusUsername = 'exosdata'
          def nexusPassword = 'stage'
          def nexusRepository = 'ExosDataComponents'
          
          sh "curl -u ${nexusUsername}:${nexusPassword} --upload-file ${componentsPath} ${nexusUrl}/repository/${nexusRepository}"
        }
      }
    }
  }
}
