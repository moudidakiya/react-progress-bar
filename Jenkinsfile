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
    stage('SonarQube Analysis') {
            steps {
              withSonarQubeEnv('SonarQube') {
                script {
                    def scannerHome = tool 'SonarQube'
                    withCredentials([usernamePassword(credentialsId: 'sonarqube-credentials', usernameVariable: 'sonarUsername', passwordVariable: 'sonarPassword')]) {
                    sh "${scannerHome}/bin/sonar-scanner " +
                    "-Dsonar.host.url=http://192.99.35.61:9000 " +
                    "-Dsonar.projectKey=reactApp " +
                    "-Dsonar.sources=. " +
                    "-Dsonar.exclusions=vendor/**,resources/**,**/*.java " +
                    "-Dsonar.login=$sonarUsername " +
                    "-Dsonar.password=$sonarPassword"
            
                    }
                }
              }
            }
        }
    stage('Publish') {
      steps {
        script {
          
          def nexusUrl = 'http://192.99.35.61:8081'
          def nexusRepository = 'ExosDataComponents' // Replace with your hosted repository name
          def nexusUsername = 'exosdata'
          def nexusPassword = 'stage'
          
          // Generate an authentication token
          def authToken = "${nexusUsername}:${nexusPassword}".bytes.encodeBase64().toString()
          
          def yourName = 'exosdata'
          def yourEmail = 'elmajoudiyasmine@gmail.com'
  
          writeFile file: '/var/lib/jenkins/workspace/first/.npmrc', text: """
          init.author.name = ${yourName}
          init.author.email = ${yourEmail}
          email = ${yourEmail}
          always-auth = true
          _auth = ${authToken}
          registry = ${nexusUrl}/repository/${nexusRepository}/
          //${nexusUrl}/repository/${nexusRepository}/:_authToken=${authToken}
"""

          
          sh "cat /var/lib/jenkins/workspace/first/.npmrc"
          // Publish the package to the custom registry
          sh "npm publish"
        
          
        }
      }
    }
  }
}
