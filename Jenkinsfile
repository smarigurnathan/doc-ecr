pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REGISTRY = '071604987339.dkr.ecr.ap-south-1.amazonaws.com'
        ECR_REPOSITORY = 'jenkins-ecr-project'
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(
                        "${ECR_REGISTRY}/${ECR_REPOSITORY}:${DOCKER_IMAGE_TAG}"
                    )
                }
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-ecr-credentials']
                ]) {
                    sh '''
                        aws ecr get-login-password \
                        --region ${AWS_REGION} | \
                        docker login \
                        --username AWS \
                        --password-stdin ${ECR_REGISTRY}
                    '''
                }
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                script {
                    docker.image(
                        "${ECR_REGISTRY}/${ECR_REPOSITORY}:${DOCKER_IMAGE_TAG}"
                    ).push()
                }
            }
        }
    }
}