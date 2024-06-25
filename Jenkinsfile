pipeline {
    agent {
        kubernetes {
            yamlFile 'build-pod.yaml'
            defaultContainer 'ez-docker-helm-build'
        }
    }

    environment {
        DOCKER_IMAGE = 'razasraf7/jenkinsfile-exercise'
        GITHUB_API_URL = 'https://api.github.com'
        GITHUB_REPO = 'RazAsraf7/jenkins_tasks'
        GITHUB_TOKEN = credentials('github_credentials')
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
                script {
                    echo "Branch Name: ${env.BRANCH_NAME}"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:latest", "--no-cache .")
                }
            }
        }
        stage('Push Docker Image') {
            when {
                expression {
                    env.BRANCH_NAME == 'main'
                }
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }
}
