#!groovy

node {
 
  def app

  stage('echo env variables') {
      sh "export PATH=${env.PATH}:/usr/bin/amazon-ecr-credential-helper/bin/local"
      sh "echo ${env.PATH}"
      sh "echo $app"
  }

  stage('Clone repository') {
    /* Let's make sure we have the repository cloned to our workspace */
      checkout scm 
  }

  stage('Build image') {
    /* This builds the actual image; synonymous to
     * docker build on the command line */
        app = docker.build("e2edemo-jenkins:${env.BUILD_NUMBER}")
  }

  stage('Test image') {
    /* Ideally, we would run a test framework against our image.
     * For this example, we're using a Volkswagen-type approach ;-) */
        app.inside {
            sh 'echo "Tests passed"'
        }
  }

  stage('Push image') {
    /* Finally, we'll push the image with two tags:
     * First, the incremental build number from Jenkins
     * Second, the 'latest' tag.
     * Pushing multiple tags is cheap, as all the layers are reused. */
        sh "echo $PATH"
        sh "aws ecr get-login --region us-east-1"
        sh "docker tag e2edemo-jenkins:${env.BUILD_NUMBER} 679404489841.dkr.ecr.us-east-1.amazonaws.com/e2edemo-jenkins:${env.BUILD_NUMBER}"
          // docker.withRegistry("https://679404489841.dkr.ecr.us-east-1.amazonaws.com", "ecr:us-east-1:dr-ttrahan-aws") {
          //   docker.image("e2edemo-jenkins").push()
          //   app.push()
          //   app.push("latest")
          // }
  }
}


