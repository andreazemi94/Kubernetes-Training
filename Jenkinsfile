pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'https://my-spring-training.sonarqube:9000' // URL del tuo SonarQube
        SONAR_AUTH_TOKEN = credentials('sonar-token') // Token di autenticazione di SonarQube
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/andreazemi94/Kubernetes-Training'  // Repository del tuo microservizio Spring
            }
        }

        stage('Build') {
            steps {
                script {
                    // Usa Maven per fare il build del microservizio Spring
                    sh 'mvn clean install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Esegui l'analisi del codice con SonarQube
                    sh "mvn sonar:sonar -Dsonar.projectKey=my-spring-app -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.login=$SONAR_AUTH_TOKEN"
                }
            }
        }
    }

    post {
        always {
            // Azioni da eseguire sempre, anche se il build fallisce
            cleanWs()
        }
    }
}