pipeline {
    agent any  // usa qualsiasi nodo disponibile

    tools {
        maven 'M3' // nome configurato in Jenkins: Manage Jenkins > Global Tool Configuration
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // prende il codice sorgente dal repository (Git)
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }

    post {
        success {
            echo 'Build completata con successo!'
        }
        failure {
            echo 'Errore nella build.'
        }
    }
}
