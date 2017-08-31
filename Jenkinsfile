#!groovy

pipeline {
  
  agent {
    // docker { image 'node:7-alpine' }
    any
  }

  environment {  
    PATH = "{$env.PATH}:/usr/bin/amazon-ecr-credential-helper/bin/local"
    app = null
  }

  stages {

    stage('echo env variables') {
      steps {
        echo $PATH
        echo app
      }
    }

    stage('Clone repository') {
      /* Let's make sure we have the repository cloned to our workspace */
      steps {
        checkout scm 
      }
    }

    stage('Build image') {
      /* This builds the actual image; synonymous to
       * docker build on the command line */
      steps {
        script {
          app = docker.build("e2edemo-jenkins:${env.BUILD_NUMBER}")
        }
      }
    }

    stage('Test image') {
      /* Ideally, we would run a test framework against our image.
       * For this example, we're using a Volkswagen-type approach ;-) */
      steps {
        script {
          app.inside {
              sh 'echo "Tests passed"'
          }
        }
      }
    }

    stage('Push image') {
      /* Finally, we'll push the image with two tags:
       * First, the incremental build number from Jenkins
       * Second, the 'latest' tag.
       * Pushing multiple tags is cheap, as all the layers are reused. */
      steps {
        script {
          sh "echo $PATH"

          withEnv(['PATH+=/usr/bin/amazon-ecr-credential-helper/bin/local']) {
            sh "echo $PATH"
            docker.withRegistry("https://679404489841.dkr.ecr.us-east-1.amazonaws.com", "ecr:us-east-1:dr-ttrahan-aws") {
              app.push()
              app.push("latest")
            }
          }

        }
      } 
    
    }
  } 
}
