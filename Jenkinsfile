pipeline {
  agent {
    docker { image 'python:3.11' } // el contenedor con python
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Install deps') {
      steps {
        bat 'pip install -r requirements.txt'
      }
    }
    stage('Run tests') {
      steps {
        bat 'mkdir -p reports && pytest --junitxml=reports/results.xml'
      }
    }
    stage('Publish results') {
      steps { junit 'reports/results.xml' }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true
    }
  }
}
