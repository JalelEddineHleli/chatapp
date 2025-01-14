pipeline {
    agent any

    environment {
        // Déclaration des variables globales
        APP_NAME = "monimage"
        DOCKER_USER = "jaleleddine1"
        DOCKER_PASS = "dockerhub" // Utilisé si vous ne gérez pas les credentials dans Jenkins directement
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = '1.0'
    }

    stages {
        stage("Checkout from SCM") {
            steps {
                // Récupération du code depuis GitHub
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/JalelEddineHleli/chatapp.git'
            }
        }

    

        stage("Build and Push to Registry") {
            steps {
                script {
                    // Construction de l'image Docker pour le backend
                    docker_image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}", "backend") // Assurez-vous que le Dockerfile est dans 'backend'
                    
                    // Utilisation des credentials Docker Hub pour pousser l'image
                    docker.withRegistry('', 'dockerhubcreds') {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push("latest") // Pousser le tag "latest"
                    }
                }
            }
        }

        stage("Deploy to Kubernetes") {
            steps {
                script {
                    // Configurer le kubeconfig pour Kubernetes
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        // Appliquer les fichiers manifestes Kubernetes pour déployer le backend
                        bat 'kubectl apply -f backend/configmap-a.yaml --validate=false'
                        bat 'kubectl apply -f backend/deployment-a.yaml'
                        bat 'kubectl rollout restart deployment monimage'  // Nom de votre déploiement
                    }
                }
            }
        }

        stage('Cleanup Artifacts') {
            steps {
                script {
                    // Nettoyer les images Docker locales après déploiement
                    bat "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    bat "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
    }
}
