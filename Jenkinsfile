pipeline {
    agent any
    environment {
        registry = "211125350773.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo"
    }
    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws']]) {
    // Your pipeline steps that require AWS credentials
}
    stages {
        stage('checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/sudha']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/sudhasanshi/docker-spring-boot.git']])
            }
        }
          stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
         stage ("Build Image") {
            steps {
                script {
                    dockerImage=docker.build registry
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
          stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 211125350773.dkr.ecr.ap-south-1.amazonaws.com"
                    sh "docker push 211125350773.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo:$BUILD_NUMBER"
                    
                }
            }
        }
        stage ("Helm deploy") {
            steps {
                script {
                    sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
                }
            }
        
    }
}
