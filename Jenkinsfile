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
        stage('Create merge request'){
            when {
                not {
                    branch 'main'
                }
            }
            steps {
                withCredentials([string(credentialsId: 'github-creds', variable: 'GITHUB_TOKEN')]) {
                    script {
                        def branchName = env.BRANCH_NAME
                        def pullRequestTitle = "Merge ${branchName} into main"
                        def pullRequestBody = "Automatically generated merge request for branch ${branchName}"

                        sh """
                            curl -X POST -H "Authorization: token ${GITHUB_TOKEN}" \
                            -d '{ "title": "${pullRequestTitle}", "body": "${pullRequestBody}", "head": "${branchName}", "base": "main" }' \
                            ${GITHUB_API_URL}/repos/${GITHUB_REPO}/pulls
                        """
                    }
                }
            }
        }
    }
}