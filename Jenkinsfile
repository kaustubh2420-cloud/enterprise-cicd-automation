pipeline {
    agent any

    environment {
        IMAGE_NAME = "enterprise-cicd-app"
        IMAGE_TAG = "build-${BUILD_NUMBER}"
        CONTAINER_NAME = "enterprise-cicd-app-${BUILD_NUMBER}"
        AWS_REGION = "ap-south-1"
        ECR_REPOSITORY = "499989195436.dkr.ecr.ap-south-1.amazonaws.com/enterprise-cicd-app"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Test Application') {
            steps {
                sh '''
                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        ${IMAGE_NAME}:${IMAGE_TAG}

                    sleep 5

                    APP_IP=$(docker inspect -f \
                        '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' \
                        ${CONTAINER_NAME})

                    curl --fail http://${APP_IP}:5000/health
                    curl --fail http://${APP_IP}:5000/version
                '''
            }
        }

        stage('Verify AWS Credentials') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'aws-jenkins-credentials',
                    usernameVariable: 'AWS_ACCESS_KEY_ID',
                    passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                )]) {
                    sh '''
                        export AWS_DEFAULT_REGION=${AWS_REGION}
                        aws sts get-caller-identity
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'docker rm -f ${CONTAINER_NAME} || true'
        }

        success {
            echo 'CI pipeline completed successfully!'
        }

        failure {
            echo 'CI pipeline failed. Check the console output.'
        }
    }
}
