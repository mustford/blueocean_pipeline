pipeline {
  agent {
    docker {
      image 'node:20.18-alpine'
    }
  }

  environment {
        //AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        //AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION = 'us-east-1'
        //BITBUCKET_ACCESS_TOKEN = credentials('bitbucket-access-token')
  }
  stages {
    stage('Build, Test, Lint') {
      parallel {
        stage('Build') {
          steps {
            sh '''
              echo 'Installing dependencies...'
              node -v
              npm -v
              npm i
            '''
          }
        }
        stage('Lint') {
          steps {
            echo 'Check code with linter...'
          }
        }
        stage('Test') {
          steps {
            echo 'Testing app'
            sh 'npm test'
          }
        }
        stage('Test Coverage') {
          steps {
            echo 'Test coverage report'
          }
        }
      }
    }
    stage('Deploy to DEV') {
      when {
        branch 'main'
      }
      steps {
        deployToAWS('dev')
      }
    }
    stage('Deploy to STAGE') {
      when {
        branch 'main'
      }
      steps {
        input message: "Deploy to Staging?", ok: "Proceed to Deploy"
        deployToAWS('stage')
      }
    }
    stage('Deploy to PROD') {
      when {
        branch 'main'
      }
      steps {
        input message: "Deploy to Prod?", ok: "Proceed to Deploy"
        deployToAWS('prod')
      }
    }
  }
}

def deployToAWS(env) {
    sh '''
    echo "Building Docker image"
    #docker build -t "blueocean:$BUILD_NUMBER" -t blueocean .

    echo "Pushing Docker image to AWS ECR"
    #aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin <ECR_REPO_URL>
    #docker tag blueocean:$BUILD_NUMBER <ECR_REPO_URL>:latest
    #docker push <ECR_REPO_URL>:latest
    '''
}