pipeline {
    agent any  // Run on any available agent (host)

    stages {
        stage('Build with Maven') {
            agent {
                docker { image 'maven:3.8.6-openjdk-11' }  // Maven Docker image
            }
            steps {
                sh 'mvn clean install'  // Build the project inside the container
            }
        }

        stage('Copy .jar to Host') {
            steps {
                script {
                    // Copy .jar file from the container's workspace to the host system
                    sh 'cp target/my-app.jar /tmp/my-app.jar'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Run Docker build command on the host system (not inside the container)
                    sh 'docker build -t my-app-image /tmp'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
