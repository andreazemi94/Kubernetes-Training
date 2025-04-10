pipeline {
    agent {
        kubernetes {
            label 'jenkins-agent' // etichetta del nodo per il pod
            defaultContainer 'kaniko' // Container predefinito da usare (il container con Kaniko)
            yaml """
				apiVersion: v1
				kind: Pod
				metadata:
				  name: jenkins-pod
				spec:
				  containers:
				  - name: kaniko
					image: gcr.io/kaniko-project/executor:latest
					command: ['cat']
					tty: true
					volumeMounts:
					- mountPath: /workspace
					  name: kaniko-volume
				  volumes:
				  - name: kaniko-volume
					persistentVolumeClaim:
					  claimName: kaniko-pvc  # Devi avere un PVC configurato in Kubernetes per questo
			"""
        }
    }

    tools {
        maven 'M3' // nome configurato in Jenkins: Manage Jenkins > Global Tool Configuration
    }

    environment {
        SONARQUBE_SCANNER_HOME = tool 'SonarQubeScanner' // Nome del tool configurato in Jenkins
        IMAGE_NAME = 'demo-app'
        IMAGE_TAG = 'latest'
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

        stage('Build Docker Image with Kaniko') {
            steps {
                container('kaniko') {
                    dir('Spring/demo') {
                        sh '''
                        /kaniko/executor \
                          --dockerfile=Dockerfile \
                          --context=dir:///workspace/Spring/demo \
                          --destination=demo-app:latest \
                          --insecure \
                          --skip-tls-verify
                        '''
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    // Applica il deployment e il servizio su Minikube
                    sh 'kubectl apply -f Spring/demo/k8s/deployment.yaml'
                    sh 'kubectl apply -f Spring/demo/k8s/service.yaml'
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
