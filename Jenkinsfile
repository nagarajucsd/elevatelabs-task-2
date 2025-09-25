pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        // Define your application name here
        APP_NAME        = 'task-app' 
        
        // Correctly define image name using the APP_NAME variable
        DOCKER_IMAGE    = "naveenrroy/${APP_NAME}" 
        
        // Use the built-in BUILD_NUMBER for a unique tag
        DOCKER_TAG      = "build-${BUILD_NUMBER}" 
        
        // Correctly load the 'dockerhub' credential. 
        // DOCKER_CREDS_USR will contain the username.
        // DOCKER_CREDS_PSW will contain the password/token.
        DOCKER_CREDS    = credentials('dockerhub') 
    }

    stages {
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/naveenroy65/task'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application...'
                sh 'mvn package'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"

                    echo 'Logging in and pushing Docker image to DockerHub...'
                    // Use the securely loaded credentials from the environment block
                    sh "echo '${DOCKER_CREDS_PSW}' | docker login -u '${DOCKER_CREDS_USR}' --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
            post {
                always {
                    // This block ensures you always log out
                    echo 'Logging out of Docker Hub...'
                    sh 'docker logout'
                }
            }
        }

        stage('Deploy to Docker Container') {
            steps {
                echo 'Deploying application as a Docker container...'
                // Use the correct DOCKER_IMAGE and DOCKER_TAG variables
                sh """
                    docker stop ${APP_NAME} || true
                    docker rm ${APP_NAME} || true
                    docker run -d --name ${APP_NAME} -p 8080:8080 ${DOCKER_IMAGE}:latest
                """
            }
        }
    }
}
