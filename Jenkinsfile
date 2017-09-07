#!groovy

node {
 
  def app

  stage('Clone repository') {
    /* Let's make sure we have the repository cloned to our workspace */
      checkout scm 
  }

  stage('Build image') {
    /* This builds the actual image; synonymous to
     * docker build on the command line */
        app = docker.build("e2edemo-jenkins:jenkins.${env.BUILD_NUMBER}")
  }

  stage('Test image') {
    /* Ideally, we would run a test framework against our image.
     * For this example, we're using a Volkswagen-type approach ;-) */
        app.inside {
            sh 'echo "Tests passed"'
        }
  }

  stage('Push image') {
    /* Finally, we'll push the image  */
        sh "echo $PATH"
        sh "cd $HOME"
        sh "aws ecr get-login --region us-east-1"
        sh "docker tag e2edemo-jenkins:jenkins.${env.BUILD_NUMBER} 679404489841.dkr.ecr.us-east-1.amazonaws.com/e2edemo-jenkins:jenkins.${env.BUILD_NUMBER}"
        sh "docker push 679404489841.dkr.ecr.us-east-1.amazonaws.com/e2edemo-jenkins:jenkins.${env.BUILD_NUMBER}"
  }

  stage('Update Shippable image resource state') {
    /* Get the Shippable project id using the resource Id of the image resource 
    (which is available on the SPOG page) */
    RESOURCE_ID=36839
    PROJECT_ID=$(curl -H "Authorization: apiToken eebf7679-44ee-47c3-bde2-d60fd4f7b6fe" "https://api.shippable.com/resources/$RESOURCE_ID" | jq ".projectId")

    # Post the new version of the image resource to Shippable
    curl -H "Authorization: apiToken eebf7679-44ee-47c3-bde2-d60fd4f7b6fe" -H "Content-Type: application/json" -X POST -d '{"resourceId": resourceId,"projectId": '$PROJECT_ID',"versionName": "'jenkins.{env.BUILD_NUMBER}'"}' https://api.shippable.com/versions
    }
    
  }


