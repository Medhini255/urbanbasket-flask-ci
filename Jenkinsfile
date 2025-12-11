pipeline {
    agent any

    environment {
        IMAGE_NAME = "urbanbasket-svc:3"
        CONTAINER_NAME = "urbanbasket-container-12088"
        PORT = "12088"
    }

    stages {

        stage('Pre-check Docker') {
            steps {
                script {
                    if (!fileExists('/usr/bin/docker') && !fileExists('/usr/bin/podman')) {
                        error("Docker not found! Install Docker before running this pipeline.")
                    }
                    sh "docker --version"
                }
            }
        }

        stage('Checkout') {
            steps {
                git url: 'https://github.com/Medhini255/urbanbasket-flask-ci.git',
                    branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Stop Previous Container') {
            steps {
                sh """
                    if [ \$(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                        docker rm -f ${CONTAINER_NAME}
                    fi
                """
            }
        }

        stage('Run Container') {
            steps {
                sh """
                    docker run -d --name ${CONTAINER_NAME} -p ${PORT}:${PORT} ${IMAGE_NAME}
                """
            }
        }
    }

    post {
        success {
            echo "UrbanBasket CI Pipeline completed successfully!"
            sh "docker ps | grep ${CONTAINER_NAME}"
        }
        failure {
            echo "UrbanBasket CI Pipeline FAILED."
        }
    }
}
