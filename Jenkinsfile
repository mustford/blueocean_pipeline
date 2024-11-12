pipeline {
  agent {
    docker {
      image 'node:20.18-slim'
    }
  }
  stages {
    stage('build') {
      steps {
        echo 'Installing dependencies...'
        sh 'node -v'
        sh 'npm -v'
        sh 'npm i'
      }
      stage('test') {
        steps {
          echo 'Testing app'
          sh 'npm test'
        }
      }
      stage('deploy') {
        steps {
          echo 'Deploying app'
          sh 'npm test'
        }
      }
    }
  }
}