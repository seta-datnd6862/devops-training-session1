pipeline {
    agent any
    
    environment {
        ENV = "dev"
    }

    stages {
        stage('Build Image') {
            agent any
            
            environment {
                TAG = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
                DOCKER_REPO = "setadatnd6862/devops-training"
                IMAGE_NAME = "$DOCKER_REPO:$TAG"
            }
            
            steps {
                script {
                    // Build Docker image
                    sh "docker build -t $IMAGE_NAME --build-arg BUILD_ENV=$ENV -f nodejs/Dockerfile nodejs/."

                    // Login to Docker Hub
                    sh "cat docker.txt | docker login -u setadatnd6862 --password-stdin"

                    // Tag Docker image
                    sh "docker tag $IMAGE_NAME $DOCKER_REPO:latest"

                    // Push Docker image to Docker Hub
                    sh "docker push $DOCKER_REPO:$TAG"
                    sh "docker push $DOCKER_REPO:latest"

                    // Remove local Docker image to reduce space on the build server
                    sh "docker rmi -f $IMAGE_NAME"
                }
            }
        }

        stage("Deploy") {
            agent any
            
            steps {
                script {
                    // Retrieve the latest Git commit hash
                    def TAG = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()

                    // Update docker-compose.yaml with the new image tag
                    sh "sed -i 's/{tag}/$TAG/g' /home/chung/Documents/devops-training-$ENV/docker-compose.yaml"

                    // Deploy using docker-compose
                    sh "docker-compose -f /home/chung/Documents/devops-training-$ENV/docker-compose.yaml up -d"
                }
            }
        }
    }
}
