pipeline {
  
  environment {
    REPOSITORY = "syft-sbom-demo"
  } // end environment
  
  agent any
  stages {
    
    stage('Checkout SCM') {
      steps {
        checkout scm
      } // end steps
    } // end stage "checkout scm"
    
    stage('Build image and tag with build number') {
      steps {
        script {
          sh 'docker build -t ${REPOSITORY}:${BUILD_NUMBER} .'
          // dockerImage = docker.build REPOSITORY + ":${BUILD_NUMBER}"
        } // end script
      } // end steps
    } // end stage "build image"
    
    stage('Generate SBOM with syft') {
      steps {
        script {
          sh 'syft -o json ${REPOSITORY}:${BUILD_NUMBER} > sbom-${BUILD_NUMBER}.json'
          // bonus points: add a step here to use grype to generate a vulnerability listing from the sbom 
          sh 'grype -o json sbom:sbom-${BUILD_NUMBER}.json > vulns-${BUILD_NUMBER}.json'
          archiveArtifacts '*.json'
        } // end script
      } // end steps
    } // end stage "generate sbom"
    

    stage('Clean up') {
      // delete the images locally
      steps {
        sh '''
          docker rmi ${REPOSITORY}:${BUILD_NUMBER}
          rm *.json
          '''
      } // end steps
    } // end stage "clean up"

    
  } // end stages
} //end pipeline
