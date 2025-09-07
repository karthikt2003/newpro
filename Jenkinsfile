pipeline {
    agent any
    tools {
        maven 'Maven-3.9.11'
    }
    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'   // must match Jenkins credentials ID
        DOCKER_IMAGE = "karthikt2003/newpro"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/karthikt2003/newpro.git'
            }
        }
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
                sh 'docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest'
            }
        }
        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}'
                    sh 'docker push ${DOCKER_IMAGE}:latest'
                }
            }
        }
    }
    post {
        success {
            echo "✅ Successfully built and pushed image ${DOCKER_IMAGE}:${BUILD_NUMBER}"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}

