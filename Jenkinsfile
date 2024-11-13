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
        MINIKUBE_IMAGE_REPO = 'localhost:5000'
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
        anyOf {
          branch 'dev'
          branch 'main'
        }
      }
      steps {
        deployToAWS('dev')
      }
    }
    stage('Deploy to STAGE') {
      when {
        anyOf {
          branch 'stage'
          branch 'main'
        }
      }
      steps {
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
    stage('Local Deploy (Minikube)') {
      when {
        branch 'main' 
      }
      steps {
        script {
          input message: "Deploy to Local?", ok: "Proceed to Deploy"
          deployToLocal('local')
        }
      }
    }
  }
}

def deployToAWS(env) {
    sh '''
    echo "Building Docker image"
    #docker build -t "blueocean:$BUILD_NUMBER" -t blueocean .
    
    #Remote operations with ECR and EKS
    echo "Pushing Docker image to AWS ECR"
    #aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin <ECR_REPO_URL>
    #docker tag blueocean:$BUILD_NUMBER <ECR_REPO_URL>:latest
    #docker push <ECR_REPO_URL>:latest

    echo "Updating Kubernetes Deployment in EKS"
    # Update kubeconfig to authenticate with the EKS cluster
    #aws eks update-kubeconfig --region $AWS_DEFAULT_REGION --name <EKS_CLUSTER_NAME>
    # Set the new image in the Kubernetes deployment
    #kubectl set image deployment/<DEPLOYMENT_NAME> <CONTAINER_NAME>=<ECR_REPO_URL>:latest
    # Verify the deployment rollout status
    #kubectl rollout status deployment/<DEPLOYMENT_NAME>
    '''
}

def deployToLocal(env) {
// Install Helm, AWS CLI, kubectl here
  sh '''
    apk update && apk add --no-cache curl bash
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh
    #curl -s "https://d1vvhvl2y92vvt.cloudfront.net/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    #unzip awscliv2.zip
    #./aws/install
    #apk add --no-cache kubectl
  '''

  // Continue with the deployment process
  sh '''
    echo "Building Docker image"
    docker build -t <MINIKUBE_IMAGE_REPO>/my-image:latest .
    docker push <MINIKUBE_IMAGE_REPO>/my-image:latest
    kubectl config use-context minikube
    helm upgrade --install my-app ./chart --set image.repository=my-app,image.tag=latest
  '''
}