pipeline {
  agent any
  stages {
    stage('Checkout SCM') {
      steps {
        git(url: 'https://github.com/askargd/cicd-pipeline.git', branch: 'main')
      }
    }

    stage('Application Build') {
      agent {
        docker {
          image 'node:18'
          reuseNode true
        }

      }
      steps {
        script {
          try {
            sh '''
echo "Starting application build..."
node --version
npm --version
chmod +x ./scripts/build.sh
./scripts/build.sh
'''
          } catch (Exception e) {
            error "Application build failed: ${e.getMessage()}"
          }
        }

      }
    }

    stage('Tests') {
      agent {
        docker {
          image 'node:18'
          reuseNode true
        }

      }
      steps {
        script {
          try {
            sh '''
chmod +x ./scripts/test.sh
./scripts/test.sh
'''
          } catch (Exception e) {
            error "Tests failed: ${e.getMessage()}"
          }
        }

      }
    }

    stage('Docker Image Build') {
      steps {
        script {
          try {
            withCredentials([usernamePassword(
              credentialsId: DOCKER_CREDENTIALS_ID,
              usernameVariable: 'DOCKER_USER',
              passwordVariable: 'DOCKER_PASS'
            )]) {
              sh """
              docker build -t \${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER} .
              docker tag \${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER} \${DOCKER_USER}/${IMAGE_NAME}:latest
              """
            }
          } catch (Exception e) {
            error "Docker image build failed: ${e.getMessage()}"
          }
        }

      }
    }

    stage('Docker Image Push') {
      steps {
        script {
          try {
            withCredentials([usernamePassword(
              credentialsId: DOCKER_CREDENTIALS_ID,
              usernameVariable: 'DOCKER_USER',
              passwordVariable: 'DOCKER_PASS'
            )]) {
              sh """
              echo '$DOCKER_PASS' | docker login -u '$DOCKER_USER' --password-stdin
              docker push \${DOCKER_USER}/${IMAGE_NAME}:${BUILD_NUMBER}
              docker push \${DOCKER_USER}/${IMAGE_NAME}:latest
              """
            }
          } catch (Exception e) {
            error "Docker image push failed: ${e.getMessage()}"
          }
        }

      }
    }

  }
  environment {
    DOCKER_CREDENTIALS_ID = '2b9ba1de-5797-4b34-985f-11cfa3918cda'
    IMAGE_NAME = 'epam'
  }
}
