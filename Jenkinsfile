pipeline {
    agent any

    environment {
        AWS_REGION     = 'us-east-1'
        AWS_ACCOUNT_ID = '844632828058'
        ECR_REPOSITORY = 'jenkins-ecr-task'
        PATH           = "C:\\Program Files\\Amazon\\AWSCLIV2;${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/dinesh-1898/jenkins-ecr-task.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t jenkins-ecr-app:latest .'
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-ecr-credentials']]) {
                    bat '''
                        aws ecr get-login-password --region %AWS_REGION% ^
                        | docker login --username AWS --password-stdin %AWS_ACCOUNT_ID%.dkr.ecr.%AWS_REGION%.amazonaws.com
                    '''
                }
            }
        }

        stage('Push to ECR') {
            steps {
                bat '''
                    docker tag jenkins-ecr-app:latest %AWS_ACCOUNT_ID%.dkr.ecr.%AWS_REGION%.amazonaws.com/jenkins-ecr-app:latest
                    docker push %AWS_ACCOUNT_ID%.dkr.ecr.%AWS_REGION%.amazonaws.com/jenkins-ecr-app:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Docker image successfully pushed to AWS ECR.'
        }
        failure {
            echo 'Jenkins ECR CI/CD Pipeline failed.'
        }
    }
}
