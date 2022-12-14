pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: "5"))
  }
  environment {
    HEROKU_API_KEY = credentials("heroku-api-key")
    IMAGE_NAME = "jenkins-example-laravel-demo"
    IMAGE_TAG = "latest"
    APP_NAME = "jenkins-example-laravel-demo"
  }
  stages {
    stage("Build") {
      steps {
        bat "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
      }
    }
    stage("Login") {
      steps {
        bat "docker login --username=_ --password=$HEROKU_API_KEY registry.heroku.com"
      }
    }
    stage("Push to Heroku registry") {
      steps {
        bat """
          docker tag $IMAGE_NAME:$IMAGE_TAG registry.heroku.com/$APP_NAME/web
          docker push registry.heroku.com/$APP_NAME/web
        """
      }
    }
    stage("Release the image") {
      steps {
          bat "heroku container:release web --app=jenkins-example-laravel-demo"
        }
    }
  }
  post {
    always {
      bat "docker logout"
    }
  }
}
