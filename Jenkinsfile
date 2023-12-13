pipeline {
    agent any
    environment {
        IMAGE_NAME = "tpdevops"
        REGISTRY   = "akrout97"
    }
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '3'))
    }

    stages {


        stage('Build Docker Image') {
            steps {
                sh "docker build --no-cache -t ${IMAGE_NAME} ."
            }
        }

        stage('Push Docker Image to registry'){
          steps{
              withCredentials([usernamePassword(credentialsId: 'docker_registry', passwordVariable: 'password', usernameVariable: 'username')]) {
                sh "docker login ${REGISTRY} -u $username -p $password"
                sh "docker tag ${IMAGE_NAME} ${REGISTRY}/${IMAGE_NAME}"
                sh "docker push ${REGISTRY}/${IMAGE_NAME}"
              }
          }
        }

        stage('Clean up'){
          steps{
              bat "docker rmi ${IMAGE_NAME} ${REGISTRY}/${IMAGE_NAME}"
              }
          }

        stage ('Run docker image'){

           steps{
            bat "docker pull ${REGISTRY}/${IMAGE_NAME}"
            bat "docker run -d --name ${IMAGE_NAME} -p 8888:80 ${REGISTRY}/${IMAGE_NAME}"
           }
        }
    }
}

