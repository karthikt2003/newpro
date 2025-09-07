pipeline {
    agent any
    tools {
        // 👇 Change this if your Jenkins Maven tool has a different name
        maven 'Maven-3.9.11'
    }
    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'  // 👈 must match Jenkins credentials ID
        DOCKER_IMAGE = "karthikt2003/newpro:${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/karthikt2003/newpro.git'
            }
        }
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                script {
                    withDockerRegistry([credentialsId: "${DOCKERHUB_CREDENTIALS}", url: '']) {
                        docker.image("${DOCKER_IMAGE}").push()
                    }
                }
            }
        }
    }
    post {
        success {
            echo "✅ Successfully built and pushed image ${DOCKER_IMAGE}"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}
