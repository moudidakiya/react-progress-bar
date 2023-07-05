pipeline {
  agent {
    docker {
      image 'node:latest'
    }
  }

  stages {
    stage('Build') {
      steps {
        sh 'npm install --cache /tmp/npm-cache'
        sh 'npm run build'
      }
    }
    
    stage('Test') {
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
          sh 'npm test'
        }
        
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
