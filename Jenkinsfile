pipeline {
    agent any
    environment {
        IMAGE_NAME        = "tpdevops"
        DOCKER_HUB_USER   = "akrout97"
    }
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '3'))
    }

    stages {


        stage('Build Docker Image') {
            steps {
                bat "docker build --no-cache -t ${IMAGE_NAME} ."
            }
        }

        stage('Push Docker Image to registry'){
          steps{
              withCredentials([usernamePassword(credentialsId: 'docker_registry', passwordVariable: 'password', usernameVariable: 'username')]) {
                bat "docker login -u $username -p $password"
                bat "docker tag ${IMAGE_NAME} ${DOCKER_HUB_USER}/${IMAGE_NAME}"
                bat "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}"
              }
          }
        }

        stage('Clean up'){
          steps{
              bat "docker rmi ${IMAGE_NAME} ${DOCKER_HUB_USER}/${IMAGE_NAME}"
              }
          }

        stage ('Run docker image'){

           steps{
            bat "docker pull ${DOCKER_HUB_USER}/${IMAGE_NAME}"
            bat "docker run -d --name ${IMAGE_NAME} -p 8888:80 ${DOCKER_HUB_USER}/${IMAGE_NAME}"
           }
        }
    }
}

