pipeline {
    agent any

    environment {
        PATH = "${env.PATH}:/bin:/usr/bin:/usr/local/bin"  // Append typical shell paths
        DOCKERHUB_CREDENTIALS = credentials('123')  // DockerHub credentials ID from Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/hsnrazaawan/task_mlops.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Use pip3 to install dependencies if Python3 is installed
                sh '/bin/sh -c "pip3 install -r requirements.txt"'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image
                sh '/bin/sh -c "docker build -t mlops-app ."'
            }
        }

        stage('Docker Login and Push') {
            steps {
                script {
                    // Login to DockerHub and push the image
                    sh '/bin/sh -c "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"'
                    sh '/bin/sh -c "docker tag mlops-app:latest ${DOCKERHUB_CREDENTIALS_USR}/mlops-app:latest"'
                    sh '/bin/sh -c "docker push ${DOCKERHUB_CREDENTIALS_USR}/mlops-app:latest"'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '/bin/sh -c "docker run -d -p 5001:5000 mlops-app"'
            }
        }
    }

     post {
        always {
            script {
                def containers = sh(script: 'docker ps -a -q', returnStdout: true).trim().split('\n')
                containers.each { containerId ->
                    sh "docker rm -f ${containerId} || true"
                }
                // Remove Docker image
                sh 'docker rmi mlops-app || true'
            }
        }
    }
}
