pipeline {
    agent any

    environment {
        IMAGE_NAME = "docker_assignment3:latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning the repository...'
                git url: 'https://github.com/ksj6/docker_jenkins.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo 'Running tests on Docker container...'
                    // Clean up any existing containers
                    sh 'docker rm -f test-container || true'
                    sh '''
                    docker run -d --name test-container -p 5000:5000 $IMAGE_NAME
                    sleep 5  # Wait for the app to start
                    curl -f http://localhost:5000 || exit 1  # Check if the app is running
                    docker stop test-container
                    docker rm test-container
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up Docker images...'
            script {
                sh 'docker rmi $IMAGE_NAME || true'
            }
        }
        failure {
            echo 'Build failed!'
        }
        success {
            echo 'Build and tests were successful!'
        }
    }
}
