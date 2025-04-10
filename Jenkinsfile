pipeline {
    agent any  // usa qualsiasi nodo disponibile

    tools {
        maven 'M3' // nome configurato in Jenkins: Manage Jenkins > Global Tool Configuration
    }

    environment {
        SONARQUBE_SCANNER_HOME = tool 'SonarQubeScanner' // Nome del tool configurato in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // prende il codice sorgente dal repository (Git)
            }
        }

        stage('Build') {
            steps {
                dir('Spring/demo') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { // Nome del server SonarQube configurato in Jenkins
                    dir('Spring/demo') {
                    sh "${SONARQUBE_SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=demo -Dsonar.sources=src -Dsonar.java.binaries=target/classes"
                    }
                }
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
