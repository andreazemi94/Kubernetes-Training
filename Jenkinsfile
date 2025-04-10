pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'https://my-spring-training.sonarqube:9000'
        SONAR_AUTH_TOKEN = credentials('sonar-token')
        MVN_HOME = tool name: 'Maven 3.8.6', type: 'maven'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/andreazemi94/Kubernetes-Training'
            }
        }

        stage("Build") {
            steps {
                git url: 'https://github.com/andreazemi94/Kubernetes-Training'
                withMaven {
                    sh "mvn clean package"
                } // withMaven will discover the generated Maven artifacts, JUnit Surefire & FailSafe reports and FindBugs reports
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    sh "${MVN_HOME}/bin/mvn sonar:sonar -Dsonar.projectKey=my-spring-app -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_AUTH_TOKEN}"
                }
            }
        }
    }

    post {
        always {
            script {
                try {
                    cleanWs()
                } catch (e) {
                    echo "cleanWs non disponibile. Plugin mancante?"
                }
            }
        }
    }
}
