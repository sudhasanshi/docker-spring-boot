pipeline {
    agent any
    environment {
        registry = "211125350773.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo"
    }
    stages {
        stage('checkout') {
            steps {
                checkout scm
            }
        }
        stage("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        stage("Build Image") {
            steps {
                script {
                    dockerImage = docker.build registry
                    dockerImage.tag(env.BUILD_NUMBER)
                }
            }
        }
        stage("Push to ECR") {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws']]) {
                        sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 211125350773.dkr.ecr.ap-south-1.amazonaws.com"
                        sh "docker push 211125350773.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo:${env.BUILD_NUMBER}"
                    }
                }
            }
        }
        stage("Helm deploy") {
            steps {
                script {
                    sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
