pipeline {
    agent any

    tools {
       // jdk 'Java11'
        maven 'Maven3'
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

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh 'mvn package'
                echo 'Application packaged successfully!'
            }
        }
    }
}
