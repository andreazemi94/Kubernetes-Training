pipeline {
    agent any
  stages {
        stage("Build") {
            steps {
                git branch: 'main',  url: 'https://github.com/andreazemi94/Kubernetes-Training'
        withMaven {
                    sh "mvn clean package"
        } // withMaven will discover the generated Maven artifacts, JUnit Surefire & FailSafe reports and FindBugs reports
      }
    }
  }
}