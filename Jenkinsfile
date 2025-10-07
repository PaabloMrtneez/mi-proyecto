pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Detect Python') {
      steps {
        script {
          // Intenta obtener la ruta de python con `where`
          def out = bat(script: 'where python', returnStdout: true).trim()
          if (!out) {
            error "Python no encontrado en el PATH del agente. Instala Python o añade su carpeta a PATH."
          }
          // `where` puede devolver varias líneas; nos quedamos con la primera
          def first = out.split("\\r?\\n")[0].trim()
          env.PYTHON = first
          echo "Usando python: ${env.PYTHON}"
        }
      }
    }

    stage('Setup venv and install deps') {
      steps {
        // Usamos la ruta completa a python para crear venv y pip
        bat """
        \"${env.PYTHON}\" -m venv venv
        call venv\\Scripts\\activate
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        """
      }
    }

    stage('Run tests') {
      steps {
        bat """
        call venv\\Scripts\\activate
        mkdir reports 2>nul || echo reports exists
        python -m pytest --junitxml=reports\\results.xml
        """
      }
    }

    stage('Publish results') {
      steps {
        junit 'reports\\results.xml'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true
    }
  }
}
