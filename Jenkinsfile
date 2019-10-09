pipeline {
  agent any
  stages {
    
    stage('git checkout')
    steps {
      script{
        sh 'git credentialsId: 'github', url: 'https://github.com/tsprasath/hello-kubernetes.git'
          }
    }
  }
    stage('Building docker image ') {
      steps {
        script {
          echo "Docker image build hello-kubernetes:1.0.0"

          sh '''

docker build -t hello-kubernetes:1.0.0 .
docker rmi hello-kubernetes:1.0.0
'''
        }

      }
    }
  }
  post {
    always {
      echo 'One way or another, I have finished'

    }

    success {
      echo 'I succeeded!'

    }

    unstable {
      echo 'I am unstable :/'

    }

    failure {
      echo 'I failed :('

    }

    changed {
      echo 'Things were different before...'

    }

  }
  options {
    timestamps()
    disableConcurrentBuilds()
  }
}
