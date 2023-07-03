def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    
    environment {
        appRegistry = '860597918607.dkr.ecr.us-east-1.amazonaws.com/java-repo'
        awsRegistry = "https://860597918607.dkr.ecr.us-east-1.amazonaws.com"
        cluster = "jenkins"
        service = "java-svc"
        AWS_ACCESS_KEY_ID = "AKIA4QX5CDOH5KSPA4FL"
        AWS_SECRET_ACCESS_KEY = "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"
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
              docker.withRegistry( awsRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
        }
        
         stage('Deploy to ECS staging') {
             steps {
                 withAWS(credentials: 'AWS_Credentials', region: 'us-east-1') {
                     sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                 } 
             }
         }
        
    }
}
