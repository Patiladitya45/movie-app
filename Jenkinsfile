pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "patiladi09/movie-app:latest"
        DOCKER_CREDENTIALS = "docker-hub-credentials" // Jenkins credentials ID
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install & Test') {
            steps {
                script {
                    if (isUnix()) {
                        sh "npm install && npm test"
                    } else {
                        bat "npm install && npm test"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh "docker build -t ${DOCKER_IMAGE} ."
                    } else {
                        bat "docker build -t %DOCKER_IMAGE% ."
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        if (isUnix()) {
                            sh """
                                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                                docker push ${DOCKER_IMAGE}
                            """
                        } else {
                            bat """
                                echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                                docker push %DOCKER_IMAGE%
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished."
        }
    }
}
