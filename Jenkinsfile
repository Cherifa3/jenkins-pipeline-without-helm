pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "cherifa3/mon-app"
        TAG = "latest"
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
                sh 'docker build -t $DOCKER_IMAGE:$TAG .'
            }
        }

        stage('Pousser Image Docker') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                    sh 'docker push $DOCKER_IMAGE:$TAG'
                }
            }
        }

        stage('Déployer sur Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }

    post {
        always {
            echo 'Pipeline terminé.'
        }
    }
}
