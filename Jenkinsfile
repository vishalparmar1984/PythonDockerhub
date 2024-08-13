pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = 'vishalparmar94@gmail.com'
        DOCKER_HUB_PASSWORD = 'Vishal@123'
        REPO = 'vishalp123/demo-python'
        GIT_REPO = 'https://github.com/vishalparmar1984/PythonDockerhub.git'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${GIT_REPO}"
            }
        }
        stage('Get Commit ID') {
            steps {
                script {
                    // Get the short commit ID (last 6 characters)
                    commitId = sh(script: "git rev-parse --short=6 HEAD", returnStdout: true).trim()
                    echo "Git Commit ID: ${commitId}"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Use the commit ID as the image tag
                    imageName = "${REPO}:${commitId}"
                    dockerImage = docker.build(imageName)
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    sh "echo ${DOCKER_HUB_PASSWORD} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin https://index.docker.io/v1/"
                    dockerImage.push()
                    sh "docker logout"
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        success {
            script {
                // Trigger the deployment job and pass the image tag as a parameter
                build job: 'demo2', parameters: [
                    string(name: 'DOCKER_IMAGE_TAG', value: "${commitId}")
                ]
            }
        }
    }
}
