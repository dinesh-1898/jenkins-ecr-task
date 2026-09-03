pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '844632828058'
        ECR_REPOSITORY = 'jenkins-ecr-task'
        IMAGE_TAG = 'v1'
    }

    stages {

        stage('Clone GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/dinesh-1898/jenkins-ecr-task.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %ECR_REPOSITORY%:%IMAGE_TAG% .'
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-ecr-credentials']
                ]) {
                    bat 'aws ecr get-login-password --region %AWS_REGION% | docker login --username AWS --password-stdin %AWS_ACCOUNT_ID%.dkr.ecr.%AWS_REGION%.amazonaws.com'
                }
            }
        }

        stage('Tag Image') {
            steps {
                bat 'docker tag %ECR_REPOSITORY%:%IMAGE_TAG% %AWS_ACCOUNT_ID%.dkr.ecr.%AWS_REGION%.amazonaws.com/%ECR_REPOSITORY%:%IMAGE_TAG%'
            }
        }

        stage('Push to ECR') {
            steps {
                bat 'docker push %AWS_ACCOUNT_ID%.dkr.ecr.%AWS_REGION%.amazonaws.com/%ECR_REPOSITORY%:%IMAGE_TAG%'
            }
        }

        stage('Verify ECR') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-ecr-credentials']
                ]) {
                    bat 'aws ecr describe-images --repository-name %ECR_REPOSITORY% --region %AWS_REGION%'
                }
            }
        }
    }
}
