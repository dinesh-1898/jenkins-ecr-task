pipeline {

    agent any
 
    environment {

        AWS_REGION = 'us-east-1'

        AWS_ACCOUNT_ID = '844632828058'

        ECR_REPOSITORY = 'jenkins-ecr-task'

        IMAGE_NAME = 'jenkins-ecr-task'
 
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}"

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

                sh 'docker build -t ${IMAGE_NAME}:latest .'

            }

        }
 
        stage('Login to ECR') {

            steps {

                withCredentials([

                    [$class: 'AmazonWebServicesCredentialsBinding',

                     credentialsId: 'aws-ecr-credentials']

                ]) {

                    sh '''

                        aws ecr get-login-password --region ${AWS_REGION} |

                        docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com

                    '''

                }

            }

        }
 
        stage('Push to ECR') {

            steps {

                sh '''

                    docker tag ${IMAGE_NAME}:latest ${ECR_URI}:latest

                    docker push ${ECR_URI}:latest

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
 
