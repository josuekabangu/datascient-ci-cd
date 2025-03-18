pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDS = credentials('docker-hub-creds') // Credentials DockerHub
        DOCKER_TAG = "v${GIT_COMMIT}" // Tag de l'image
        KUBE_NAMESPACE = "" // Namespace dynamique
    }

    stages {
        // Étape 1 : Cloner le dépôt Git
        stage('Clonage du dépôt Git...') {
            steps {
                echo 'Clonage du dépôt Git...'
                git branch: "${BRANCH_NAME}", url: 'https://github.com/josuekabangu/datascient-ci-cd.git'
                script {
                    //Définir le namespace kubernetes en fonction de la branche
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
                    echo "Namespace kubernetes détécté : ${KUBE_NAMESPACE}"
                }
            }
        }

        // Étape 2 : Construire les images Docker
        stage('Construction des images docker') {
            steps {
                script {
                    echo "Construiction des images Docker avec docker-compose..."
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
    }
}
