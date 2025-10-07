pipeline {
  agent any
  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Setup venv and install deps (Windows)') {
      steps {
        bat '''
        python -m venv venv
        call venv\\Scripts\\activate
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        '''
      }
    }

    stage('Run tests') {
      steps {
        bat '''
        call venv\\Scripts\\activate
        python -m pytest --junitxml=reports\\results.xml
        '''
      }
    }

    stage('Publish results') {
      steps {
        junit 'reports\\results.xml'
      }
    }
  }
  post { always { archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true } }
}
