pipeline {
  agent {
    docker {
      image 'node:16' // Updated to use Node.js version 16
      args '-u root'
    }
  }
  environment {
    npm_config_cache = '/var/tmp/npm-cache'
  }

  stages {
    stage('Build') {
      steps {
        sh 'node --version'
        sh 'npm install'
        sh 'rm -rf dist/*'
        sh 'npm cache clean --force'
        sh 'rm -rf node_modules/.cache/rollup-plugin-typescript2'
        sh 'npm run build'
      }
    }
    stage('Debug') {
      steps {
        sh 'cat package.json' // Display the contents of package.json
        sh 'ls'
        sh 'cd dist && ls'
        sh 'ls -l ./dist/index.d.ts'
        sh 'ls -l ./dist/index.js'
        sh 'ls -l ./dist/index.js.map'
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
          def nexusRepository = 'ExosDataComponents/'
          def nexusUrl2='http://192.99.35.61:8081/'

          
          sh "npm config set registry http://192.99.35.61:8081/repository/yasmine/"
          sh "curl -u ${nexusUsername}:${nexusPassword} ${nexusUrl2}"
          withCredentials([usernamePassword(passwordVariable: 'stage', usernameVariable: 'exosdata')]) {
            // Publish the package to the custom registry
            sh "npm publish"}

          // Publish the package
          sh "npm publish"

          // Upload individual files if necessary
          sh "curl -u ${nexusUsername}:${nexusPassword} --upload-file ./dist/index.d.ts ${nexusUrl}/repository/${nexusRepository}"
          sh "curl -u ${nexusUsername}:${nexusPassword} --upload-file ./dist/index.js ${nexusUrl}/repository/${nexusRepository}"
          sh "curl -u ${nexusUsername}:${nexusPassword} --upload-file ./dist/index.js.map ${nexusUrl}/repository/${nexusRepository}"
        }
      }
    }
  }
}
