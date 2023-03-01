def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }
    
    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = '334671708617.dkr.ecr.us-east-1.amazonaws.com/myregistory'
        AWSRegistry = "https://334671708617.dkr.ecr.us-east-1.amazonaws.com/myregistory"
    }

    stages {
        stage('Build App Image') {
            steps {
                script {
                    dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", "./Dockerfiles/App/")
                }
            }
        }
        
        stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( AWSRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
        }
    }
}
