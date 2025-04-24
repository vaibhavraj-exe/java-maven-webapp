pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'demo-app'
        DOCKER_REGISTRY = 'docker.io'   // Change to your Docker Hub registry if needed
        DOCKER_HUB_USERNAME = 'your-docker-hub-username'  // Replace with your Docker Hub username
        DOCKER_HUB_PASSWORD = 'your-docker-hub-password'  // Replace with your Docker Hub password
        AWS_EC2_INSTANCE_IP = 'your-ec2-instance-ip'  // Replace with your AWS EC2 instance IP
        AWS_EC2_USERNAME = 'ec2-user'  // Or your EC2 username
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the repository code from GitHub (or another source control)
                git 'https://github.com/your-username/your-repo.git'  // Replace with your repo URL
            }
        }

        stage('Build and Test') {
            steps {
                script {
                    // Build and run Maven tests
                    sh 'mvn clean install'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image from Dockerfile
                    sh 'docker build -t $DOCKER_IMAGE_NAME .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub
                    sh "echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin"

                    // Push the Docker image to Docker Hub
                    sh 'docker tag $DOCKER_IMAGE_NAME $DOCKER_REGISTRY/$DOCKER_HUB_USERNAME/$DOCKER_IMAGE_NAME'
                    sh 'docker push $DOCKER_REGISTRY/$DOCKER_HUB_USERNAME/$DOCKER_IMAGE_NAME'
                }
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                script {
                    // SSH into your EC2 instance and deploy the Docker container
                    sh """
                    ssh -o StrictHostKeyChecking=no $AWS_EC2_USERNAME@$AWS_EC2_INSTANCE_IP <<EOF
                    docker pull $DOCKER_REGISTRY/$DOCKER_HUB_USERNAME/$DOCKER_IMAGE_NAME
                    docker run -d -p 9090:8080 $DOCKER_REGISTRY/$DOCKER_HUB_USERNAME/$DOCKER_IMAGE_NAME
                    EOF
                    """
                }
            }
        }
    }
    
    post {
        always {
            // Clean up (optional) or additional actions
            echo 'Cleaning up resources...'
        }
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Something went wrong!'
        }
    }
}
