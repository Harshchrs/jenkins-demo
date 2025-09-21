pipeline {
  agent any
  environment {
    IMAGE = "batman/my-app:${BUILD_NUMBER}" // Docker image name with build number
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Install & Test') {
      steps {
        sh 'npm ci'
        sh 'npm test'
      }
    }
    stage('Build Docker') {
      steps { sh 'docker build -t $IMAGE .' }
    }
    stage('Push Docker') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
        usernameVariable: 'DOCKER_USER',
        passwordVariable: 'DOCKER_PASS')]) {
          sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
          sh 'docker push $IMAGE'
        }
      }
    }
    stage('Deploy Locally') {
      steps {
        sh 'docker stop my-app || true'
        sh 'docker rm my-app  || true'
        sh 'docker run -d --name my-app -p 3000:3000 $IMAGE'
      }
    }
  }
}
