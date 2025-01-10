pipeline {
  agent any
  stages {
    stage('checkout') {
      steps {
        sh 'git checkout test'
      }
    }

    stage('Build') {
      steps {
        sh '''chmod +x scripts/build.sh
scripts/build.sh'''
      }
    }

    stage('Test') {
      steps {
        sh 'scripts/test.sh'
      }
    }

    stage('Docker build') {
      steps {
        script {
          docker build -t mybuildimage
        }

      }
    }

  }
}