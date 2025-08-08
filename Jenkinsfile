pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: nodejs
    image: node:18-alpine
    command:
    - cat
    tty: true
  - name: docker
    image: docker:20.10.16-dind
    securityContext:
      privileged: true
"""
    }
  }

  environment {
    REGISTRY = "nysominea/cicd-nextjs"
    IMAGE_TAG = "latest"
  }

  stages {
    stage('Build Docker Image') {
      steps {
        container('docker') {
          sh """
          docker build -t $REGISTRY:$IMAGE_TAG .
          """
        }
      }
    }

    stage('Docker Login & Push') {
      steps {
        container('docker') {
          withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh '''
              echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
              docker push $REGISTRY:$IMAGE_TAG
            '''
          }
        }
      }
    }
  }
}
