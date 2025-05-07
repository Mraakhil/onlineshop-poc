// @Library('shared') _

pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'devshubh2204/onlineshop-poc'
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
        GITHUB_CREDENTIALS = credentials('git-hub-cred')
        GIT_BRANCH = "feature"
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                script {
                    clean_ws()
                }
            }
        }

        stage('Checkout Code') {
            steps {
                script {
                    checkout_code(
                         git url: 'https://github.com/Mraakhil/dockertheend.git', branch: 'main'
                    )
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker_build(
                        imageName: DOCKER_IMAGE_NAME,
                        imageTag: DOCKER_IMAGE_TAG,
                        dockerfile: 'Dockerfile',
                        context: '.'
                    )
                }
            }
        }

        stage('Security Scan with Trivy') {
            steps {
                script {
                    trivy()
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker_push(
                        imageName: DOCKER_IMAGE_NAME,
                        imageTag: DOCKER_IMAGE_TAG,
                        credentials: 'docker-hub-cred'
                    )
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    runDockerContainer(
                        image: 'devshubh2204/onlineshop-poc:latest',
                        ports: '3000:80'
                    )
                }
            }
        }
    }
}
