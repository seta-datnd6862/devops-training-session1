pipeline {
    agent {
        label 'Training-test'
    }

    environment {
        ENV = "dev"
    }

    stages {
        stage('Build Image') {
            agent {
                label 'Training-test'
            }
            
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
                    sh "cat docker.txt | docker login -u dat.nguyen@seta-international.vn --password-stdin"

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
            agent {
                label 'Training-test'
            }

            environment {
                TAG = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
                DOCKER_REPO = "setadatnd6862/devops-training"
                IMAGE_NAME = "$DOCKER_REPO:$TAG"
            }
            
            steps {
                script {
                    // Build Docker image
                    sh "docker pull $IMAGE_NAME"

                    sh "docker run -p '8000:8000' $IMAGE_NAME"
                }
            }
        }
    }
}
