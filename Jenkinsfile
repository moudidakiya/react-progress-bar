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
        sh 'ls'
        sh 'cd dist && ls'
        sh 'ls'
        sh 'cd src'
        sh 'ls'
        sh 'cd ..'
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
          def nexusUrl = 'http://192.99.35.61:8081'
          def nexusUsername = 'exosdata'
          def nexusPassword = 'stage'
          def nexusRepository = 'ExosDataComponents'
          
          sh "curl -u ${nexusUsername}:${nexusPassword} --upload-file ${componentsPath} ${nexusUrl}/repository/${nexusRepository}"
          sh "cd ${componentsPath} && curl -u ${nexusUsername}:${nexusPassword} --upload-file ./* ${nexusUrl}/repository/${nexusRepository}"
          sh "curl -u exosdata:stage --upload-file ./index.d.ts --upload-file ./index.js --upload-file ./index.js.map http://192.99.35.61:8081/repository/ExosDataComponents/"


        }
      }
    }
  }
}
