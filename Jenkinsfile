Jenkinsfile(Declarative Pipeline)
pipeline {
  agent any
  stages {
    stage('Ready') {
      steps {
        echo 'Get ready step'
      }
    }
  }
  post {
    always {
      echo 'I did the always'
    }  
  }
}
