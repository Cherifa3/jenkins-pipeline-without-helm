pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'cherifa3/mon-app'
        DOCKER_HOST = 'unix:///var/run/docker.sock'
    }
    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'main', 
                url: 'https://github.com/Cherifa3/jenkins-pipeline-without-helm.git'
            }
        }
        stage('Construire Image Docker') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }
        stage('Pousser limage Docker') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-creds', 
                        passwordVariable: 'DOCKER_PASSWORD', 
                        usernameVariable: 'DOCKER_USERNAME'
                    )]) {
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }
        stage('Déployer sur Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}
