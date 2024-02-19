pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = '353700960133.dkr.ecr.us-east-1.amazonaws.com/java'
        awsRegistry = "https://353700960133.dkr.ecr.us-east-1.amazonaws.com"
        cluster = "Stagging-Environment"
        service = "java-app"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'cb0db832-0de4-469e-897f-327ba907dfad', 
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
                    
                        sh '''
                        echo 'Push to Repo'
                        docker push hemakumarux/cicd-e2e:${BUILD_NUMBER}
                        sudo apt-get update
                        // install aws cli 
                        sudo apt-get install -y awscli
                        aws --version
                        // authenticate aws ECR
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 353700960133.dkr.ecr.us-east-1.amazonaws.com
                        docker tag hemakumarux/cicd-e2e:${BUILD_NUMBER} 353700960133.dkr.ecr.us-east-1.amazonaws.com/my-ecr-repo:v ${BUILD_NUMBER}
                        docker push 353700960133.dkr.ecr.us-east-1.amazonaws.com/my-ecr-repo:v ${BUILD_NUMBER}
                        '''
                    
                }
            }
        }     
        //stage('deploy to ecs'){            
        //    steps{               
        //        withAWS(credentials: 'awscreds', region: 'us-east-1') {
        //            sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
        //        }
        //        }
        //}        
    }
}
