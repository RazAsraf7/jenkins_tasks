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
        stage('Create Pull Request') {
            when {
                not {
                    branch 'main'
                }
            }
            steps {
                script {
                    def pullRequestTitle = "Merge ${env.BRANCH_NAME} to main"
                    def pullRequestBody = "This is an automated pull request from Jenkins."
                    
                    def data = [
                        title: pullRequestTitle,
                        head: env.BRANCH_NAME,
                        base: 'main',
                        body: pullRequestBody
                    ]

                    def json = new groovy.json.JsonBuilder(data).toPrettyString()

                    httpRequest(
                        url: "${GITHUB_API_URL}/repos/${GITHUB_REPO}/pulls",
                        httpMode: 'POST',
                        contentType: 'APPLICATION_JSON',
                        customHeaders: [[name: 'Authorization', value: "token ${GITHUB_TOKEN}"]],
                        requestBody: json,
                        validResponseCodes: '201'
                    )
                }
            }
        }
    }
}