pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '943755888791'
        ECR_REPOSITORY = 'aws-devops-app'
        IMAGE_NAME = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Rakeshchintu123/aws-devops-cicd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $ECR_REPOSITORY:$BUILD_NUMBER .'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                    aws ecr get-login-password --region $AWS_REGION |
                    docker login --username AWS --password-stdin \
                    $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    docker tag $ECR_REPOSITORY:$BUILD_NUMBER \
                    $IMAGE_NAME:$BUILD_NUMBER

                    docker push $IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker pull $IMAGE_NAME:$BUILD_NUMBER

                    docker stop aws-devops-container || true
                    docker rm aws-devops-container || true

                    docker run -d \
                        --name aws-devops-container \
                        -p 80:80 \
                        $IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }
    }
}