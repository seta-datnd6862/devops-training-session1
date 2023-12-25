pipeline {
    agent any
    environment {
        ENV = "dev"
        NODE = "Build-server"
    }

    stages {
        stage('Build Image') {
            agent {
                node any
                environment {
                    TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
                }
                steps {
                    sh "docker build nodejs/. -t devops-training-$ENV:latest --build-arg BUILD_ENV=$ENV -f nodejs/Dockerfile"

                    sh "cat docker.txt | docker login -u setadatnd6862 --password-stdin"
                    // tag docker image
                    sh "docker tag devops-training-$ENV:latest setadatnd6862/devops-training:$TAG"

                    //push docker image to docker hub
                    sh "docker push setadatnd6862/devops-training:$TAG"

                    // remove docker image to reduce space on build server	
                    sh "docker rmi -f setadatnd6862/devops-training:$TAG"

                }
                
            }
        }
        stage ("Deploy") {
            agent {
                node any
                environment {
                    TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
                }
                steps {
                    sh "sed -i 's/{tag}/$TAG/g' /home/chung/Documents/devops-training-$ENV/docker-compose.yaml"
                    sh "docker compose up -d"
                }      
            }
        }
    }
}
