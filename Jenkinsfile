pipeline {

  agent none

  environment {
    DOCKER_IMAGE = "nhtua/flask-docker"
  }

  stages {
    stage("Test") {
      agent {
          docker {
            args '-u 1000:1000tmp:/root/.cache'
            image 'python:3.8-slim-buster'
          }
      }
      steps {
        sh "pip install poetry"
        sh "poetry install"
        sh "poetry run pytest"
      }
    }
  }
}
