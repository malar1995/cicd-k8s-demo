pipeline {
  environment {
    registry = "interviewdot/cicd-k8s-demo"
    registryCredential = 'docker-hub-credentials'
    dockerImage = ''
  }
  agent any
  tools {
          maven 'Maven 3.3.9'
      }
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/net-vinothkumar/cicd-k8s-demo.git'
      }
    }
    stage('Compile') {
          steps {
          sh '''
                echo "PATH = ${PATH}"
                echo "M2_HOME = ${M2_HOME}"
             '''
            sh 'mvn clean install'
          }
        }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image') {
      steps{
        script {
          /* Finally, we'll push the image with two tags:
                   * First, the incremental build number from Jenkins
                   * Second, the 'latest' tag.
                   * Pushing multiple tags is cheap, as all the layers are reused. */
          docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
              dockerImage.push("${env.BUILD_NUMBER}")
              dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy to Kubernetes'){
        steps{
            sh 'kubectl apply -f deployment.yml'
       }
    }
  }
}