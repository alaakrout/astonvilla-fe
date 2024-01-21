pipeline {
    agent any
    environment {
        IMAGE_NAME        = "astonvilla-fe"
        DOCKER_HUB_USER   = "akrout97"
    }
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '3'))
    }

    stages {


        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Push Docker Image to registry.'){
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

        stage ('deploy app to minikube'){

           steps{
                withKubeConfig([credentialsId: 'kubeconfig', serverUrl: 'https://192.168.59.102:8443']) {
                  bat 'kubectl delete -f astonvilla-fe.yaml'
                  bat 'kubectl apply -f astonvilla-fe.yaml'
                  bat 'kubectl rollout restart deployment astonvilla-fe-deployment'
                }
           }
        }
    }
}

