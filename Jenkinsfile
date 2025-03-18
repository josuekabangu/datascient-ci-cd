pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDS = credentials('docker-hub-creds') // Credentials DockerHub
        DOCKER_TAG = "v${GIT_COMMIT}" // Tag de l'image
        KUBE_NAMESPACE = "" // Namespace dynamique
    }

    stages {
        // Étape 1 : Cloner le dépôt Git
        stage('Clonage du dépôt Git') {
            steps {
                echo 'Clonage du dépôt Git...'
                git branch: "${BRANCH_NAME}", url: 'https://github.com/josuekabangu/datascient-ci-cd.git'
                script {
                    // Définir le namespace kubernetes en fonction de la branche
                    if (env.BRANCH_NAME == 'develop') {
                        KUBE_NAMESPACE = 'dev'
                        echo "Déploiement dans le namespace : ${KUBE_NAMESPACE}"
                    } else if (env.BRANCH_NAME == 'qa') {
                        KUBE_NAMESPACE = 'qa'
                        echo "Déploiement dans le namespace : ${KUBE_NAMESPACE}"
                    } else if (env.BRANCH_NAME == 'staging') {
                        KUBE_NAMESPACE = 'staging'
                        echo "Déploiement dans le namespace : ${KUBE_NAMESPACE}"
                    } else if (env.BRANCH_NAME == 'main') {
                        KUBE_NAMESPACE = 'prod'
                        echo "Déploiement dans le namespace : ${KUBE_NAMESPACE}"
                    } else {
                        error("Branche non supportée : ${BRANCH_NAME}")
                    }
                    echo "Namespace Kubernetes détecté : ${KUBE_NAMESPACE}"
                }
            }
        }

        // Étape 2 : Vérification de la version Docker Compose (facultatif)
        stage('Vérification de Docker Compose') {
            steps {
                sh 'docker-compose --version'
                sh 'docker --version'
            }
        }

        // Étape 3 : Construire les images Docker
        stage('Construction des images Docker') {
            steps {
                script {
                    echo "Construction des images Docker avec docker-compose..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASS')]) {
                        sh '''
                            echo "Connexion à Docker Hub..."
                            docker login -u $DOCKER_HUB_USER -p $DOCKER_HUB_PASS
                            echo "Construction des images..."
                            docker compose build
                        '''
                    }
                }
            }
        }

        // Étape 4 : Déploiement Kubernetes (facultatif)
        stage('Déploiement Kubernetes') {
            steps {
                script {
                    echo "Déploiement de l'application dans Kubernetes..."
                    withCredentials([kubeconfigFile(credentialsId: 'kube-config', variable: 'KUBECONFIG')]) {
                        sh '''
                            kubectl apply -f deployment.yaml --namespace=${KUBE_NAMESPACE}
                        '''
                    }
                }
            }
        }
    }
}
