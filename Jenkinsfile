pipeline {

  agent none

  environment {
    DOCKER_IMAGE = "nhtua/flask-docker"
  }

  stages {
    stage("Test") {
      agent {
          docker {
            image 'python:3.8-slim-buster'
            args '-u 0:0 -v /tmp:/root/.cache'
          }
      }
      steps {
        sh "pip install poetry"
        sh "poetry install"
        sh "poetry run pytest"
      }
    }

    stage("build") {
      agent { node {label 'master'}}
      environment {
        DOCKER_TAG="${GIT_BRANCH.tokenize('/').pop()}-${BUILD_NUMBER}-${GIT_COMMIT.substring(0,7)}"
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
            sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
        }

        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} . "
        sh "docker image tag ${DOCKER_IMAGE}:${DOCKER_TAG} thienlan1004/devops_v1:${DOCKER_TAG}"
        sh "docker push thienlan1004/devops_v1:${DOCKER_TAG}"
        sh "echo "Test pull""
        script {
          if (GIT_BRANCH ==~ /.*master.*/) {
            sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
            sh "docker image tag ${DOCKER_IMAGE}:${DOCKER_TAG} thienlan1004/devops_v1:${DOCKER_TAG}"
            sh "docker push thienlan1004/devops_v1:${DOCKER_TAG}"
          }
        }

        //clean to save disk
        sh "docker image rm ${DOCKER_IMAGE}:${DOCKER_TAG}"
      }
    }
  }

  post {
    success {
      echo "SUCCESSFUL"
    }
    failure {
      echo "FAILED"
    }
  }
}
