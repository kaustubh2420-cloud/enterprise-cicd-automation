pipeline {
    agent any

    environment {
        IMAGE_NAME = "enterprise-cicd-app"
        IMAGE_TAG = "build-${BUILD_NUMBER}"
        CONTAINER_NAME = "enterprise-cicd-app-${BUILD_NUMBER}"
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
}                  sleep 5

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
