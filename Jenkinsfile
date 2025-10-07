pipeline {
  agent { docker { image 'python:3.11' } }
  stages {
    stage('Tests') {
      steps {
        bat 'pip install -r requirements.txt'
        bat 'pytest --junitxml=reports/results.xml'
      }
    }
  }
}
