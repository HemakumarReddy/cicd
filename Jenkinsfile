pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = '211125716680.dkr.ecr.us-east-1.amazonaws.com/hh-java'
        awsRegistry = "https://211125716680.dkr.ecr.us-east-1.amazonaws.com"
        cluster = "Stagging-Environment"
        service = "java-app"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'debb8179-e4c3-4352-9fc7-48537c407669', 
                url: 'https://github.com/HemakumarReddy/cicd',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t hemakumarux/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    docker.withRegistry( awsRegistry, registryCredential ){
                        sh '''
                        echo 'Push to Repo'
                        docker push hemakumarux/cicd-e2e:${BUILD_NUMBER}
                        '''
                     }
                }
            }
        }     
        stage('deploy to ecs'){            
            steps{               
                withAWS(credentials: 'awscreds', region: 'us-east-1') {
                    sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                }
                }
        }        
    }
}
