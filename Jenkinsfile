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

        // Étape 2 : Vérification de Docker et Docker Compose (facultatif)
        stage('Vérification de Docker') {
            steps {
                sh 'docker --version'  // Vérifier la version de Docker
            }
        }

        // Étape 3 : Construire l'image Docker à partir du Dockerfile
        stage('Construction de l\'image Docker') {
            steps {
                script {
                    echo "Construction de l'image Docker avec le Dockerfile..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASS')]) {
                        sh '''
                            echo "Connexion à Docker Hub..."
                            docker login -u $DOCKER_HUB_USER -p $DOCKER_HUB_PASS
                            echo "Construction de l'image Docker..."
                            docker build -t $DOCKER_HUB_USER/datascient-ci-cd:$DOCKER_TAG .
                        '''
                    }
                }
            }
        }

        // Étape 4 : Pousser l'image Docker sur Docker Hub
        stage('Push de l\'image Docker sur Docker Hub') {
            steps {
                script {
                    echo "Pousser l'image Docker sur Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASS')]) {
                        sh '''
                            docker push $DOCKER_HUB_USER/datascient-ci-cd:$DOCKER_TAG
                        '''
                    }
                }
            }
        }

        // Étape 5 : Déploiement Kubernetes
        stage('Déploiement Kubernetes') {
            steps {
                script {
                    echo "Déploiement de l'application dans Kubernetes..."
                    withCredentials([kubeconfigFile(credentialsId: 'kube-config', variable: 'KUBECONFIG')]) {
                        sh '''
                            # Appliquer la configuration Kubernetes pour déployer l'application
                            kubectl apply -f deployment.yaml --namespace=${KUBE_NAMESPACE}
                            
                            # Mettre à jour le déploiement avec la nouvelle image Docker
                            kubectl set image deployment/datascient-ci-cd datascient-ci-cd=$DOCKER_HUB_USER/datascient-ci-cd:$DOCKER_TAG --namespace=${KUBE_NAMESPACE}
                        '''
                    }
                }
            }
        }
    }
}
