pipeline {
  agent any

  environment {
    IMAGE_NAME = "paulbouwer/hello-kubernetes"
    REGISTRY_CREDENTIALS = 'dockerhub'
    GIT_CREDENTIALS = 'github-ssh'
    BRANCH = 'main'
  }

  triggers {
    githubPush()
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Push Docker Image') {
      steps {
        script {
          docker.withRegistry('', REGISTRY_CREDENTIALS) {
            def img = docker.build("${IMAGE_NAME}:${env.BUILD_NUMBER}", "--build-arg IMAGE_VERSION=${env.BUILD_NUMBER} .")
            img.push()
          }
        }
      }
    }

    stage('Bump Kustomize Tag & Push') {
      steps {
        sshagent([GIT_CREDENTIALS]) {
          sh '''
            git config user.email "jenkins@example.com"
            git config user.name "Jenkins"
            sed -i "s|newTag:.*|newTag: ${BUILD_NUMBER}|" yaml/overlays/prod/kustomization.yaml
            git add yaml/overlays/prod/kustomization.yaml
            git commit -m "chore: bump image tag to ${BUILD_NUMBER}"
            git push origin ${BRANCH}
          '''
        }
      }
    }
  }

  post {
    success {
      echo "Pipeline succeeded: ${IMAGE_NAME}:${BUILD_NUMBER}"
    }
    failure {
      echo "Pipeline failed."
    }
  }
}
