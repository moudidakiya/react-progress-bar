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
   stage('Debug') {
      steps {
        sh 'cat package.json' // Display the contents of package.json
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
          def componentsPath = 'dist'
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
