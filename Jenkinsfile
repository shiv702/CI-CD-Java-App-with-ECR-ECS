pipeline {
    agent any
    
    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        appRegistry = '860597918607.dkr.ecr.us-east-1.amazonaws.com/java-repo'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${appRegistry}:${BUILD_NUMBER}", "./Dockerfiles/App")
                    
                    withDockerRegistry([credentialsId: 'aws_creds', url: 'https://860597918607.dkr.ecr.us-east-1.amazonaws.com']) {
                        sh "docker push ${appRegistry}:${BUILD_NUMBER}"
                    }
                }
            }
        }
        
        stage('Deploy to ECS') {
            steps {
                catchError {
                    script {
                        withAWS(credentials: 'aws_creds', region: 'us-east-1') {
                            ecsDeploy(
                                 clusterName: 'jenkins',
                                 serviceName: 'javacode',
                                 taskDefinition: 'javacode',
                                 containerName: 'java-container',
                                 image: "${appRegistry}:${BUILD_NUMBER}"
                        ])
                    }
                }
            }
        }
    }
}
