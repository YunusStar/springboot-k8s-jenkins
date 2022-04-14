pipeline {
    agent { label "master" }
    environment {
        // YOU SET THE ENVIRONMENT VARIABLES HERE
        DOCKER_REGISTRY = "dolphinstar"
        APP_REPO_NAME= "springboot"
        PATH="/usr/local/bin/:${env.PATH}"
        DockerUsername = dolphinstar121
        DockerPassword = ***********
    }
    stages {
        stage('Build') {
            steps {
                container('maven') {
                    sh 'mvn package'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build --force-rm -t "$DOCKER_REGISTRY/$APP_REPO_NAME:latest" .'
                sh 'docker image ls'
            }
        }
        stage('Push Image to Dockerhub') {
            steps {
                sh 'docker login --username $DockerUsername --password-stdin "$DockerPassword"'
                sh 'docker push "$DOCKER_REGISTRY/$APP_REPO_NAME:latest"'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker login --username $DockerUsername --password-stdin "$DockerPassword"'
                sh 'docker pull "$DOCKER_REGISTRY/$APP_REPO_NAME:latest"'
                sh 'docker rm -f helloapp'
                sh 'envsubst < ${WORKSPACE}/deployment.yaml'
                sh 'echo "Deploying the Hello World Application"'
                sh 'kubectl apply -f .'
            }
        }
    }
    post {
        success {
            echo 'Deployed Successfully'
        }
        always {
            echo 'Deleting all local images'
            sh 'docker image prune -af'
        }
    }
}
