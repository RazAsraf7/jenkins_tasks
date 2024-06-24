pipeline {
    agent {
        kubernetes{
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
            }
        }
        stage('Build Docker Image') {
            steps {
                dockerImage = docker.build("${DOCKER_IMAGE}:latest", "--no-cache .")
            }
        }
    }
}