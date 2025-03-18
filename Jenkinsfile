pipeline {
    agent any

    stages {
        stage('Deploy to Production') {
            when {
                expression { 
                    return env.BRANCH_NAME == 'develop'
                }
            }
            steps {
                echo "Déploiement sur production"
                // Ajouter ici les étapes de déploiement
            }
        }
    }
}
