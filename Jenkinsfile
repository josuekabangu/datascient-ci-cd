pipeline {
    agent any 

    stages {
        stage('Build') {
            steps {
                script {
                    //Exemple d'utilisation de BRANCH_NAME
                    if (env.BRANCH_NAME == 'main') {
                        echo "Déploiement sur production"
                        // Ajouter des étapes pour déployer sur production
                    } else {
                        echo "Pas de déploiement sur cette branche : ${env.BRANCH_NAME}"
                    }
                }
            }
        }
    }
}
