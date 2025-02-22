pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "cprathap/jenkins-k8s-app"
        KUBE_CONFIG = "/root/.kube/config"
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/prathapchitra/jenkins-k8s-app.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Building Docker Image..."
                docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .
                '''
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh '''
                    echo "Pushing Docker Image..."
                    docker push $DOCKER_IMAGE:$BUILD_NUMBER
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                echo "Deploying to Kubernetes..."
                kubectl set image deployment/jenkins-k8s-app jenkins-k8s-app=$DOCKER_IMAGE:$BUILD_NUMBER --kubeconfig=$KUBE_CONFIG
                kubectl rollout status deployment/jenkins-k8s-app --kubeconfig=$KUBE_CONFIG
                '''
            }
        }
    }
}

