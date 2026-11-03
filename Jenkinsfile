pipeline {
    agent any

    environment {
        IMAGE_NAME = "enterprise-cicd-app"
        IMAGE_TAG = "build-${BUILD_NUMBER}"
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
                        --name ${IMAGE_NAME}-${BUILD_NUMBER} \
                        -p 5002:5000 \
                        ${IMAGE_NAME}:${IMAGE_TAG}

                    sleep 5

                    curl --fail http://localhost:5002/health
                    curl --fail http://localhost:5002/version
                '''
            }
        }
    }

    post {
        always {
            sh '''
                docker rm -f ${IMAGE_NAME}-${BUILD_NUMBER} || true
            '''
        }

        success {
            echo 'CI pipeline completed successfully!'
        }

        failure {
            echo 'CI pipeline failed. Check the console output.'
        }
    }
}
