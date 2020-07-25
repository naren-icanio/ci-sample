pipeline {

  agent {
    docker { image 'ruby:2.7' }
  }

  environment {
    PLATFORM_CREDENTIALS = credentials("heroku-auth-token")
    RAINFOREST_TOKEN = credentials("rainforest-api-token")
  }

  stages {

    stage("install and build") {
      steps {
        echo "installing and setting up the application"
        sh "gem install bundler"
        sh "bundle install"

        // Install the latest stable version of rainforest-cli.
        // This will allow us to trigger a Rainforest run before we deploy.
        sh "wget 'https://bin.equinox.io/c/htRtQZagtfg/rainforest-cli-stable-linux-amd64.tgz'"
        sh "tar xvf rainforest-cli-stable-linux-amd64.tgz"
      }
    }

    stage("test") {
      steps {
        echo "testing the application"
        // Run unit tests
        sh "bundle exec rake"

        // Run Rainforest tests
        // Trigger Rainforest run and wait for results
        sh "./rainforest run --run-group 7597 --token ${RAINFOREST_TOKEN}"
      }
    }

    stage("deploy_develop") {
      when {
        expression {
          BRANCH_NAME == "develop"
        }
      }
      steps {
        echo "deploying the application to develop branch"
      }
    }

    stage("deploy_master") {
      when {
        expression {
          BRANCH_NAME == "master"
        }
      }
      steps {
        echo "deploying the application to master branch"
      }
    }

  }

  post {
    failure {
      echo "Oh my heck, my release failed"
    }
  }

}
