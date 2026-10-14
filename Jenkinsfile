pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        IMAGE_NAME = 'devops-test'
        CONTAINER_NAME = 'web-test'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                bat 'docker build -t "%IMAGE_NAME%:%BUILD_NUMBER%" .'
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                    docker rm -f "%CONTAINER_NAME%" 
                    docker run -d \
                      --name "%CONTAINER_NAME%" \
                      --restart unless-stopped \
                      -p 8080:80 \
                      "%IMAGE_NAME%:%BUILD_NUMBER%"
                '''
            }
        }

        stage('Verify') {
            steps {
                bat '''
                    sleep 2
                    curl --fail --silent --show-error http://127.0.0.1:8080 
                    echo "Application is responding on port 80."
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully."
        }
        failure {
            echo "Pipeline failed. Review the stage logs above."
        }
    }
}
