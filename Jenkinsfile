pipeline {
    agent any

    environment {
        KUBE_NAMESPACE = "" //Namespace dynamique
    }

    stages {
        stage('Build') {
            steps {
                script {
                    //Exemple d'utilisation de BRANCH_NAME
                    if (env.BRANCH_NAME == 'develop') {
                        KUBE_NAMESPACE = 'dev'
                        echo "Déploiement sur le namespace : ${KUBE_NAMESPACE}"
                       
                    } else if (env.BRANCH_NAME == 'qa') {
                        KUBE_NAMESPACE = 'QA'
                        echo "Déploiement sur le namespace : ${KUBE_NAMESPACE}"
                    } else if (env.BRANCH_NAME == 'staging') {
                        KUBE_NAMESPACE = 'staging'
                        echo "Déploiement sur le namespace : ${KUBE_NAMESPACE}"
                    } else if (env.BRANCH_NAME == 'main') {
                        KUBE_NAMESPACE = 'prod'
                        echo "Déploiement sur le namespace : ${KUBE_NAMESPACE}"
                    }
                    else {
                        echo "Pas de déploiement sur cette branche : ${env.BRANCH_NAME}"
                    }
                }
            }
        }
    }
}
