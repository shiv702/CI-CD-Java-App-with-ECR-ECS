pipeline {
    agent any
    
    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        appRegistry = '860597918607.dkr.ecr.us-east-1.amazonaws.com/java-repo:latest'
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
                    def dockerImageTag = ("java-repo:${BUILD_NUMBER}", "./Dockerfiles/App")
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        sh "docker login -u AWS -p \"${AWS_SECRET_ACCESS_KEY}\" ${appRegistry}:${BUILD_NUMBER}"
                        sh "docker push ${appRegistry}:${BUILD_NUMBER}"
                    }
                }
            }
        }
        
        stage('Deploy to ECS') {
            steps {
                script {
                    ecsTask([
                        cluster: 'jenkins',
                        taskDefinition: 'javacode',
                        container: 'java-container',
                        image: "${appRegistry}:${BUILD_NUMBER}",
                        awsAccessKeyId: 'AKIA4QX5CDOH5KSPA4FL',
                        awsSecretAccessKey: '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',
                        awsRegion: 'us-east-1'
                    ])
                }
            }
        }
    }
}
