pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        IMAGE_NAME = 'jayasurya0199/staragilefinancev1'
        REPO_URL = 'https://github.com/Jayasurya0199/projectSA1.git'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: env.REPO_URL
            }
        }

        stage('Build Java Application') {
            steps {
                script {
                    // Build the JAR file
                    sh "mvn clean package -DskipTests"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using the created JAR file
                    docker.build(env.IMAGE_NAME)
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', env.DOCKERHUB_CREDENTIALS) {
                        docker.image(env.IMAGE_NAME).push("latest")
                    }
                }
            }
        }

        stage('Pull and Run Docker Container') {
            steps {
                script {
                    sh """
                    sudo docker pull $IMAGE_NAME:latest
                    sudo docker run -itd --name staragilefinance-app -p 8085:80 $IMAGE_NAME:latest
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh "sudo docker rm -f staragilefinance-app || true"
            sh "sudo docker rmi -f $IMAGE_NAME:latest || true"
        }
    }
}
