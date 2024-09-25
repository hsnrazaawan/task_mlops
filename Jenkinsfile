pipeline {
    agent any

    environment {
        PATH = "${env.PATH}:/bin:/usr/bin:/usr/local/bin"  // Append typical shell paths
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
                   
                    withCredentials([usernamePassword(credentialsId: '123', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                       
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                        sh 'docker tag mlops-app:latest $DOCKER_USERNAME/mlops-app:latest'
                        sh 'docker push $DOCKER_USERNAME/mlops-app:latest'
                    }
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
                
                sh 'docker rmi mlops-app || true'
            }
        }
    }
}
