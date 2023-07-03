pipeline {
    agent any
    
    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
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
                    sh 'docker build -t 860597918607.dkr.ecr.us-east-1.amazonaws.com/java-repo:latest .'
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        sh 'docker login -u AWS -p "$(AWS_SECRET_ACCESS_KEY)" 860597918607.dkr.ecr.us-east-1.amazonaws.com/java-repo:latest'
                        sh 'docker push 860597918607.dkr.ecr.us-east-1.amazonaws.com/java-repo:latest'
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
                        image: '860597918607.dkr.ecr.us-east-1.amazonaws.com/java-repo:latest',
                        awsAccessKeyId: 'AKIA4QX5CDOH5KSPA4FL',
                        awsSecretAccessKey: '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',
                        awsRegion: 'us-east-1'
                    ])
                }
            }
        }
    }
}
